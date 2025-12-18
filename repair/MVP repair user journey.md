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
    %% STEP 1: LANDING & ADDRESS ENTRY
    %% =============================================
    StartNode([User Lands at RepairBot]) --> HO_Entry[Enter Address<br>and Describe Issue]:::user

    %% =============================================
    %% STEP 2: EXISTING CUSTOMER CHECK
    %% =============================================
    HO_Entry --> Dec_Existing{Existing<br>Customer?}:::logic

    %% --- NEW CUSTOMER PATH ---
    Dec_Existing -- No --> Sys_Check{High Confidence<br>in HVAC Details?}:::logic

    %% --- EXISTING CUSTOMER PATH ---
    Dec_Existing -- Yes --> Dec_FullyCovered{On Fully<br>Covered Plan?}:::logic

    Dec_FullyCovered -- Yes --> Flag_NoPay[Flag: No Payment Required]:::system
    Flag_NoPay --> Dec_Emergency

    Dec_FullyCovered -- No --> Dec_Warranty{Within 1-Year<br>Labor Warranty?}:::logic
    Dec_Warranty -- Yes --> Flag_LaborWarranty[Flag: Labor Covered<br>Parts may apply]:::system
    Flag_LaborWarranty --> Dec_Emergency

    Dec_Warranty -- No --> Dec_PartsWarranty{Within 5-10 Year<br>Parts Warranty?}:::logic
    Dec_PartsWarranty -- Yes --> Flag_PartsWarranty[Flag: Parts Covered<br>Labor billable]:::system
    Flag_PartsWarranty --> Dec_Emergency
    Dec_PartsWarranty -- No --> Dec_Emergency

    %% =============================================
    %% STEP 3: EQUIPMENT IDENTIFICATION - New Customers
    %% =============================================
    Sys_Check -- Yes --> Dec_Emergency

    Sys_Check -- No --> Bot_Ask[What heating system<br>do you have?]:::system
    Bot_Ask --> HO_Know{User Knows?}:::logic

    HO_Know -- No --> Bot_Guide[Guide user to answer]:::system
    Bot_Guide --> HO_Input

    HO_Know -- Yes --> HO_Input[Select Heating Type<br>Fuel and AC Status]:::user
    HO_Input --> Sys_Twin[Generate Digital Twin]:::system
    Sys_Twin --> Dec_Emergency

    %% =============================================
    %% STEP 4: EMERGENCY CHECK
    %% =============================================
    Dec_Emergency{Emergency<br>Visit Needed?}:::logic

    Dec_Emergency -- Yes --> Flag_Emergency[Flag: Emergency Visit<br>Premium Pricing]:::alert
    Flag_Emergency --> Bot_Triage

    Dec_Emergency -- No --> Bot_Triage

    %% =============================================
    %% STEP 5: TRIAGE
    %% =============================================
    Bot_Triage[Triage Symptoms<br>and Safety Check]:::system
    Bot_Triage --> Dec_Sched{Clicks Schedule<br>Visit Button?}:::logic

    %% --- NO SCHEDULE PATH: OFFER CHAT REPORT ---
    Dec_Sched -- No --> Dec_WantReport{Want Chat<br>Report Emailed?}:::logic
    Dec_WantReport -- No --> ExitNode([Exit Session]):::terminator
    Dec_WantReport -- Yes --> HO_Report_Contact[Enter Contact Info<br>Name and Email]:::user
    HO_Report_Contact --> Sys_SendReport[Send Chat Report<br>via Email]:::system
    Sys_SendReport --> ExitNode

    Dec_Sched -- Yes --> Sys_Cal[Display Calendar]:::system

    %% =============================================
    %% STEP 6: SCHEDULING
    %% =============================================
    Sys_Cal --> HO_Slot[Select Time Slot]:::user
    HO_Slot --> HO_Contact[Input Contact Info<br>Name, Email, Phone]:::user

    %% =============================================
    %% STEP 7: BOOKING CONFIRMATION
    %% =============================================
    HO_Contact --> HO_Book[Confirm Booking]:::user

    HO_Book --> HO_Dash[Go to HO Dashboard]:::system
    HO_Book --> Sys_Notif[Send Confirmation<br>Email and Text]:::system

    HO_Dash --> Confirm_Loop
    Sys_Notif --> Confirm_Loop

    %% =============================================
    %% STEP 8: CONFIRMATION WINDOW
    %% =============================================
    Confirm_Loop[[Confirm Visit Subprocess]]:::subprocess
    Confirm_Loop --> Dec_Resched{Reschedule?}:::logic

    Dec_Resched -- No --> Visit_Confirmed

    %% --- HO RESCHEDULE PATH ---
    Dec_Resched -- HO Reschedules --> HO_Cancel[Cancel Current Slot]:::user
    HO_Cancel --> HO_Resched_Now{Reschedule<br>Immediately?}:::logic

    HO_Resched_Now -- Yes --> HO_NewSlot[Select New Time]:::user
    HO_NewSlot --> Sys_Notify_CO[Notify CO: Job Moved]:::system
    Sys_Notify_CO --> Confirm_Loop

    HO_Resched_Now -- No --> Proc_Reengage
    Proc_Reengage[[Re-engagement Loop]]:::subprocess
    Proc_Reengage --> Dec_Reengage{HO Reschedules?}:::logic
    Dec_Reengage -- Yes --> HO_NewSlot
    Dec_Reengage -- No --> End_Unresponsive([Unresponsive]):::terminator

    %% --- CO RESCHEDULE PATH ---
    Dec_Resched -- CO Reschedules --> CO_NewTime[CO Proposes New Time]:::co
    CO_NewTime --> Sys_Notify_HO[Notify HO: Time Change<br>Offer alternative slots]:::system
    Sys_Notify_HO --> Dec_HO_Accept{HO Accepts<br>New Time?}:::logic
    Dec_HO_Accept -- Yes --> Confirm_Loop
    Dec_HO_Accept -- No --> Proc_Reengage

    %% =============================================
    %% STEP 9: ARRIVAL & NO-SHOW CHECK
    %% =============================================
    Visit_Confirmed([Visit Confirmed]):::terminator
    Visit_Confirmed --> CO_Arrive[CO Arrives at Home]:::co
    CO_Arrive --> CO_Checkin[CO Checks In via App]:::co
    CO_Checkin --> Dec_HO_Home{HO Home?}:::logic

    %% --- HO NO-SHOW PATH ---
    Dec_HO_Home -- No --> CO_Wait[CO Waits and Attempts Contact]:::co
    CO_Wait --> Dec_NoShow{HO Responds?}:::logic
    Dec_NoShow -- No --> Sys_Log_NoShow[Log No-Show]:::system
    Sys_Log_NoShow --> Proc_Reengage
    Dec_NoShow -- Yes --> CO_Diagnose

    Dec_HO_Home -- Yes --> CO_Diagnose

    %% =============================================
    %% STEP 10: DIAGNOSIS
    %% =============================================
    CO_Diagnose[CO Diagnoses Issue]:::co
    CO_Diagnose --> HO_View[Review Diagnosis<br>and Repair Scope]:::user

    %% =============================================
    %% STEP 11: REPAIR DECISION
    %% =============================================
    HO_View --> Dec_Repair{Repair Decision?}:::logic

    %% Path A: No Repair - Diagnostic Fee Only
    Dec_Repair -- Decline --> HO_Pay_Diag[Pay Diagnostic Fee]:::user
    HO_Pay_Diag --> Sys_Pay

    %% Path B: Approve Repair
    Dec_Repair -- Approve --> HO_Approve[Approve Scope]:::user

    %% Path C: Replace - Handoff to Ecom
    Dec_Repair -- Replace --> Ecom[Ecom Flow]:::system
    Ecom --> Dec_Deposit{Pay Deposit?}:::logic
    Dec_Deposit -- Yes --> Proc_Delivery[[Delivery Subprocess]]:::subprocess
    Dec_Deposit -- No --> Dec_Repair

    %% =============================================
    %% STEP 12: PARTS CHECK
    %% =============================================
    HO_Approve --> Dec_Parts{Parts Needed?}:::logic

    Dec_Parts -- No --> Dec_SameDay{Same-Day<br>Repair Possible?}:::logic

    Dec_Parts -- Yes --> Sys_Order[Tetra Orders Parts]:::tetra
    Sys_Order --> Sys_Parts_ETA[Notify HO: Parts ETA]:::system
    Sys_Parts_ETA --> Parts_Arrive[Parts Arrive]:::system
    Parts_Arrive --> Sched_Followup

    %% =============================================
    %% STEP 13: SAME-DAY REPAIR CHECK
    %% =============================================
    Dec_SameDay -- Yes --> CO_Execute

    %% --- REPAIR CANT BE DONE TODAY ---
    Dec_SameDay -- No --> Sys_Explain[Explain: Repair requires<br>additional time or resources]:::system
    Sys_Explain --> Sched_Followup

    %% --- SCHEDULE FOLLOW-UP ---
    Sched_Followup[[Schedule Follow-Up Visit]]:::subprocess
    Sched_Followup --> HO_FollowSlot[Select Follow-Up Time]:::user
    HO_FollowSlot --> Sys_Followup_Confirm[Confirm Follow-Up<br>Send Notifications]:::system
    Sys_Followup_Confirm --> CO_Execute

    %% =============================================
    %% STEP 14: EXECUTION & COMPLETION
    %% =============================================
    CO_Execute[CO Performs Repair]:::co
    CO_Execute --> HO_Sign[Sign Off on Work]:::user
    HO_Sign --> HO_Pay[Enter or Confirm Payment]:::user

    %% =============================================
    %% STEP 15: PAYMENT & CLOSE
    %% =============================================
    HO_Pay --> Dec_Payment_Flag{Payment<br>Flag Set?}:::logic

    Dec_Payment_Flag -- Fully Covered --> Sys_NoPay[Skip Payment<br>Plan Benefit]:::system
    Sys_NoPay --> Sys_Report

    Dec_Payment_Flag -- Labor Warranty --> Sys_PartsOnly[Charge Parts Only]:::system
    Sys_PartsOnly --> Sys_Pay

    Dec_Payment_Flag -- Parts Warranty --> Sys_LaborOnly[Charge Labor Only]:::system
    Sys_LaborOnly --> Sys_Pay

    Dec_Payment_Flag -- No Flag --> Sys_Pay[Process Full Payment]:::system

    Sys_Pay --> Sys_Report[Send Report to HO]:::system
    Sys_Report --> End_Journey([Journey Complete]):::terminator