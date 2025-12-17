```mermaid

flowchart TD
    %% --- STYLING ---
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef co fill:#fff8e1,stroke:#ffa000,color:#5d4037,stroke-width:2px
    classDef system fill:#f5f5f5,stroke:#333333,color:#333333,stroke-width:1px,stroke-dasharray: 5 5
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef alert fill:#fff0e6,stroke:#d9534f,color:#d9534f,stroke-width:2px
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef tetra fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20,stroke-width:2px
    classDef subprocess fill:#f4f4f4,stroke:#333333,stroke-width:2px

    %% =============================================
    %% STEP 1: JOB ASSIGNMENT
    %% =============================================
    StartNode([HO Books Visit]) --> Sys_ST[Job Created in ServiceTitan]:::system
    Sys_ST --> Sys_Flags[Job Includes Customer Flags:\nExisting Customer Status\nWarranty Status\nEmergency Flag]:::system
    Sys_Flags --> CO_Cal[Appointment Appears\non CO Calendar]:::co

    %% =============================================
    %% STEP 2: EMERGENCY HANDLING
    %% =============================================
    CO_Cal --> Dec_Emergency{Emergency\nFlag Set?}:::logic

    Dec_Emergency -- Yes --> CO_Priority[Prioritize Job\nPremium Rate Applies]:::co
    CO_Priority --> Confirm_Loop

    Dec_Emergency -- No --> Confirm_Loop

    %% =============================================
    %% STEP 3: CONFIRMATION WINDOW
    %% =============================================
    Confirm_Loop[[Confirmation Loop]]:::subprocess
    Confirm_Loop --> Dec_Resched{Reschedule?}:::logic

    Dec_Resched -- No --> CO_Arrive

    %% --- CO RESCHEDULE PATH ---
    Dec_Resched -- CO Reschedules --> CO_NewTime[CO Proposes New Time]:::co
    CO_NewTime --> Sys_Notify_HO[Notify HO: Time Change\nOffer alternative slots]:::system
    Sys_Notify_HO --> Dec_HO_Accept{HO Accepts?}:::logic
    Dec_HO_Accept -- Yes --> Confirm_Loop
    Dec_HO_Accept -- No --> Proc_Reengage

    %% --- HO CANCELS PATH ---
    Dec_Resched -- HO Cancels --> Proc_Reengage
    Proc_Reengage[[HO Re-engagement Loop]]:::subprocess
    Proc_Reengage --> Dec_Reengage{HO Reschedules?}:::logic
    Dec_Reengage -- Yes --> Confirm_Loop
    Dec_Reengage -- No --> Sys_Remove[Remove from CO Calendar]:::system
    Sys_Remove --> End_Cancelled([Job Cancelled]):::terminator

    %% =============================================
    %% STEP 4: ARRIVAL & NO-SHOW CHECK
    %% =============================================
    CO_Arrive[Arrive at Home]:::co
    CO_Arrive --> CO_Checkin[Check-in via App]:::co
    CO_Checkin --> Dec_HO_Home{HO Home?}:::logic

    %% --- HO NO-SHOW PATH ---
    Dec_HO_Home -- No --> CO_Wait[Wait and Attempt Contact]:::co
    CO_Wait --> Dec_NoShow{HO Responds?}:::logic
    Dec_NoShow -- No --> Sys_Log_NoShow[Log No-Show]:::system
    Sys_Log_NoShow --> Proc_Reengage
    Dec_NoShow -- Yes --> CO_Diagnose

    Dec_HO_Home -- Yes --> CO_Diagnose

    %% =============================================
    %% STEP 5: DIAGNOSIS
    %% =============================================
    CO_Diagnose[Diagnose Issue\nQ and A, Photos, Readings]:::co
    CO_Diagnose --> Sys_Gen[Generate Recommended\nFix and Line Items]:::system
    Sys_Gen --> CO_Review[Review and Edit\nLine Items]:::co
    CO_Review --> CO_Present[Review Scope with HO]:::co

    %% =============================================
    %% STEP 6: REPAIR DECISION
    %% =============================================
    CO_Present --> Dec_Repair{HO Decision?}:::logic

    %% Path A: No Repair - Diagnostic Fee Only
    Dec_Repair -- Decline Repair --> CO_Collect_Diag[Collect Diagnostic Fee]:::co
    CO_Collect_Diag --> Dec_Diag_Payment{Payment\nFlag Set?}:::logic
    Dec_Diag_Payment -- Fully Covered --> Sys_Skip_Diag[Skip Diagnostic Fee\nPlan Benefit]:::system
    Sys_Skip_Diag --> End_DiagOnly([Diagnostic Complete]):::terminator
    Dec_Diag_Payment -- No Flag --> Sys_Collect_Diag[Process Diagnostic Fee]:::system
    Sys_Collect_Diag --> End_DiagOnly

    %% Path B: Approve Repair
    Dec_Repair -- Approve Repair --> Dec_Parts{Parts Needed?}:::logic

    %% Path C: Replace - Handoff to Ecom
    Dec_Repair -- Replace --> End_Ecom([Handoff to Ecom]):::terminator

    %% =============================================
    %% STEP 7: PARTS CHECK
    %% =============================================
    Dec_Parts -- No --> Dec_SameDay{Same-Day\nRepair Possible?}:::logic

    Dec_Parts -- Yes --> Tetra_Order[Tetra Orders Parts]:::tetra
    Tetra_Order --> Sys_Parts_ETA[Notify HO and CO:\nParts ETA]:::system
    Sys_Parts_ETA --> Parts_Arrive[Parts Arrive\nat CO Warehouse]:::system
    Parts_Arrive --> Sched_Loop

    %% =============================================
    %% STEP 8: SAME-DAY REPAIR CHECK
    %% =============================================
    Dec_SameDay -- Yes --> CO_Execute

    Dec_SameDay -- No --> CO_Explain[Explain to HO:\nRepair requires additional\ntime or resources]:::co
    CO_Explain --> Sched_Loop

    %% --- SCHEDULE FOLLOW-UP ---
    Sched_Loop[[Schedule Follow-Up]]:::subprocess
    Sched_Loop --> Sys_ST_Followup[Create Follow-Up\nin ServiceTitan]:::system
    Sys_ST_Followup --> HO_FollowSlot[HO Selects Follow-Up Time]:::user
    HO_FollowSlot --> Sys_Followup_Confirm[Confirm Follow-Up\nNotify CO]:::system
    Sys_Followup_Confirm --> CO_Execute

    %% =============================================
    %% STEP 9: EXECUTION
    %% =============================================
    CO_Execute[Perform Repair Work]:::co
    CO_Execute --> CO_Photo[Take Verification Photos]:::co
    CO_Photo --> CO_Complete[Mark Complete in App]:::co

    %% =============================================
    %% STEP 10: COMPLETION & PAYMENT
    %% =============================================
    CO_Complete --> HO_Sign[HO Signs Off]:::user
    HO_Sign --> HO_Pay[HO Confirms Payment]:::user

    HO_Pay --> Dec_Payment_Flag{Payment\nFlag Set?}:::logic

    Dec_Payment_Flag -- Fully Covered --> Sys_NoPay[Skip Payment\nPlan Benefit]:::system
    Sys_NoPay --> Sys_Report

    Dec_Payment_Flag -- Labor Warranty --> Sys_PartsOnly[Charge Parts Only]:::system
    Sys_PartsOnly --> Sys_Pay

    Dec_Payment_Flag -- Parts Warranty --> Sys_LaborOnly[Charge Labor Only]:::system
    Sys_LaborOnly --> Sys_Pay

    Dec_Payment_Flag -- No Flag --> Sys_Pay[Process Full Payment]:::system

    Sys_Pay --> Sys_Report[Send Report\nto HO and CO]:::system
    Sys_Report --> CO_Paid[CO Receives Payment]:::co
    CO_Paid --> End_Journey([Journey Complete]):::terminator