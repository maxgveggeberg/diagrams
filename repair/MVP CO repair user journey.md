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
    Sys_ST --> Sys_Flags[Job Includes Customer Flags:<br>Existing Customer Status<br>Warranty Status<br>Emergency Flag]:::system
    Sys_Flags --> CO_Cal[Appointment Appears<br>on CO Calendar]:::co

    %% =============================================
    %% STEP 2: EMERGENCY HANDLING
    %% =============================================
    CO_Cal --> Dec_Emergency{Emergency<br>Flag Set?}:::logic

    Dec_Emergency -- Yes --> Tetra_Call_CO[Tetra Calls CO:<br>Alert Emergency Visit<br>Confirm Availability]:::tetra
    Tetra_Call_CO --> Dec_CO_Avail{CO<br>Available?}:::logic
    Dec_CO_Avail -- Yes --> CO_Priority[CO Prioritizes Job<br>Premium Rate Applies]:::co
    Dec_CO_Avail -- No --> Sys_Reassign[Reassign to<br>Available CO]:::system
    Sys_Reassign --> CO_Priority
    CO_Priority --> CO_Review_Handoff

    Dec_Emergency -- No --> CO_Review_Handoff

    %% =============================================
    %% STEP 3: HANDOFF DOCUMENT REVIEW
    %% =============================================
    CO_Review_Handoff[CO Reviews<br>Handoff Document]:::co
    CO_Review_Handoff --> Dec_Has_Parts{Has Required<br>Parts/Equipment?}:::logic
    Dec_Has_Parts -- Yes --> Confirm_Loop
    Dec_Has_Parts -- No --> CO_Get_Parts[CO Gets Required<br>Parts/Equipment]:::co
    CO_Get_Parts --> Confirm_Loop

    %% =============================================
    %% STEP 4: CONFIRMATION WINDOW
    %% =============================================
    Confirm_Loop[[Confirmation Loop]]:::subprocess
    Confirm_Loop --> Dec_Resched{Reschedule?}:::logic

    Dec_Resched -- No --> CO_Arrive

    %% --- CO RESCHEDULE PATH (System-driven) ---
    Dec_Resched -- CO Reschedules --> Sys_Pull_Avail[Pull CO Availability<br>from ServiceTitan]:::system
    Sys_Pull_Avail --> Sys_Notify_HO[Notify HO: Time Change<br>Offer alternative slots]:::system
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
    %% STEP 5: ARRIVAL & NO-SHOW CHECK
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
    %% STEP 6: DIAGNOSIS
    %% =============================================
    CO_Diagnose[Diagnose Issue<br>Q and A, Photos, Readings]:::co
    CO_Diagnose --> Sys_Gen[Generate Recommended<br>Fix and Line Items]:::system
    Sys_Gen --> CO_Review[Review and Edit<br>Line Items]:::co

    %% =============================================
    %% STEP 7: PAYMENT FLAG CHECK (Before Scope Presentation)
    %% =============================================
    CO_Review --> Dec_Payment_Flag_Scope{Payment<br>Flag Set?}:::logic

    Dec_Payment_Flag_Scope -- Fully Covered --> CO_Present_NoPay[Present Scope:<br>No Payment Required<br>Plan Benefit]:::co
    Dec_Payment_Flag_Scope -- Labor Warranty --> CO_Present_PartsOnly[Present Scope:<br>Parts Only<br>Labor Covered]:::co
    Dec_Payment_Flag_Scope -- Parts Warranty --> CO_Present_LaborOnly[Present Scope:<br>Labor Only<br>Parts Covered]:::co
    Dec_Payment_Flag_Scope -- No Flag --> CO_Present_Full[Present Scope:<br>Full Price]:::co

    CO_Present_NoPay --> Dec_Repair
    CO_Present_PartsOnly --> Dec_Repair
    CO_Present_LaborOnly --> Dec_Repair
    CO_Present_Full --> Dec_Repair

    %% =============================================
    %% STEP 8: REPAIR DECISION
    %% =============================================
    Dec_Repair{HO Decision?}:::logic

    %% Path A: No Repair - Diagnostic Fee Only
    Dec_Repair -- Decline Repair --> CO_Collect_Diag[Collect Diagnostic Fee]:::co
    CO_Collect_Diag --> Dec_Diag_Paid{HO Pays<br>Diagnostic?}:::logic
    Dec_Diag_Paid -- Yes --> Sys_Diag_Pay[Process Diagnostic Fee]:::system
    Sys_Diag_Pay --> End_DiagOnly([Diagnostic Complete]):::terminator
    Dec_Diag_Paid -- No --> Flag_Diag_Unpaid[Flag: Diagnostic Unpaid]:::system
    Flag_Diag_Unpaid --> End_DiagOnly

    %% Path B: Approve Repair
    Dec_Repair -- Approve Repair --> HO_Approve[HO Approves Scope<br>and Payment]:::user
    HO_Approve --> Dec_Parts{Parts Needed?}:::logic

    %% Path C: Replace Only
    Dec_Repair -- Replace --> CO_Set_Lead[CO Sets Replacement Lead]:::co
    CO_Set_Lead --> Sys_CO_Commission[Pay CO Lead Commission]:::system
    Sys_CO_Commission --> End_Ecom([Handoff to Ecom]):::terminator

    %% Path D: Repair AND Replace
    Dec_Repair -- Repair + Replace --> CO_Set_Lead_Both[CO Sets Replacement Lead]:::co
    CO_Set_Lead_Both --> Sys_CO_Commission_Both[Pay CO Lead Commission]:::system
    Sys_CO_Commission_Both --> HO_Approve

    %% =============================================
    %% STEP 9: PARTS CHECK
    %% =============================================
    Dec_Parts -- No --> Dec_SameDay{Same-Day<br>Repair Possible?}:::logic

    Dec_Parts -- Yes --> Alert_Tetra[Alert Tetra:<br>Parts Needed]:::tetra
    Alert_Tetra --> Tetra_Order[Tetra Orders Parts]:::tetra
    Tetra_Order --> Sys_Parts_ETA[Notify HO and CO:<br>Parts ETA]:::system
    Sys_Parts_ETA --> Parts_Arrive[Parts Arrive<br>at CO Warehouse]:::system
    Parts_Arrive --> Sched_Loop

    %% =============================================
    %% STEP 10: SAME-DAY REPAIR CHECK
    %% =============================================
    Dec_SameDay -- Yes --> CO_Execute

    Dec_SameDay -- No --> CO_Explain[Explain to HO:<br>Repair requires additional<br>time or resources]:::co
    CO_Explain --> Sched_Loop

    %% --- SCHEDULE FOLLOW-UP (Reordered) ---
    Sched_Loop[[Schedule Follow-Up]]:::subprocess
    Sched_Loop --> HO_FollowSlot[HO Selects Follow-Up Time]:::user
    HO_FollowSlot --> Sys_ST_Followup[Create Follow-Up<br>in ServiceTitan]:::system
    Sys_ST_Followup --> Confirm_Loop

    %% =============================================
    %% STEP 11: EXECUTION
    %% =============================================
    CO_Execute[Perform Repair Work]:::co
    CO_Execute --> CO_Photo[Take Verification Photos]:::co
    CO_Photo --> CO_Complete[Mark Complete in App]:::co

    %% =============================================
    %% STEP 12: COMPLETION & PAYMENT (After Repair)
    %% =============================================
    CO_Complete --> HO_Sign[HO Signs Off]:::user
    HO_Sign --> CO_Collect_Payment[CO Collects Payment]:::co
    CO_Collect_Payment --> Dec_HO_Pays{HO Pays?}:::logic

    %% --- PAYMENT COLLECTED ---
    Dec_HO_Pays -- Yes --> Sys_Pay[Process Payment]:::system
    Sys_Pay --> Sys_Report[Send Report<br>to HO and CO]:::system
    Sys_Report --> CO_Paid[CO Receives Payment]:::co
    CO_Paid --> End_Journey([Journey Complete]):::terminator

    %% --- PAYMENT NOT COLLECTED ---
    Dec_HO_Pays -- No --> CO_Log_NoPay[CO Logs: Payment<br>Not Collected]:::co
    CO_Log_NoPay --> CO_No_Pay_Yet[CO Does Not<br>Receive Payment Yet]:::co
    CO_No_Pay_Yet --> Tetra_Collection[Tetra Reaches Out<br>for Payment Collection]:::tetra
    Tetra_Collection --> Dec_Collection{Payment<br>Collected?}:::logic
    Dec_Collection -- Yes --> Sys_Pay_Late[Process Late Payment]:::system
    Sys_Pay_Late --> CO_Paid_Late[CO Receives Payment]:::co
    CO_Paid_Late --> End_Journey_Late([Journey Complete - Late Pay]):::terminator
    Dec_Collection -- No --> Flag_Unpaid[Flag: Unpaid<br>Send to Collections]:::system
    Flag_Unpaid --> End_Unpaid([Unpaid - Collections]):::terminator