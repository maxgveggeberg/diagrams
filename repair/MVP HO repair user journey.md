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
    %% STEP 1: ENTRY POINTS
    %% =============================================
    StartNode_Web([User Lands at RepairBot]) --> HO_Entry[Enter Address<br>and Describe Issue]:::user
    
    StartNode_Call([HO Calls In]) --> Dec_Answer{Call<br>Answered?}:::logic
    Dec_Answer -- Yes --> Sys_Collect_Contact[Collect Contact Info:<br>Phone Number<br>Address]:::system
    Sys_Collect_Contact --> Dec_Already_Scheduled_Phone{Already<br>Scheduled?}:::logic
    
    %% --- ALREADY SCHEDULED - PHONE ---
    Dec_Already_Scheduled_Phone -- Yes --> Show_Status_Phone[Show Booking Status:<br>Date, Time, CO Assigned]:::system
    Show_Status_Phone --> Dec_Want_Resched_Phone{Want to<br>Reschedule?}:::logic
    Dec_Want_Resched_Phone -- Yes --> HO_Cancel
    Dec_Want_Resched_Phone -- No --> End_Status_Check([Call Complete]):::terminator
    
    Dec_Already_Scheduled_Phone -- No --> HO_Entry
    
    %% --- CALLBACK FLOW ---
    Dec_Answer -- No --> Tetra_Callback[Tetra Calls<br>Customer Back]:::tetra
    Tetra_Callback --> Dec_Callback_Answer{Customer<br>Answers?}:::logic
    Dec_Callback_Answer -- Yes --> Sys_Collect_Contact
    Dec_Callback_Answer -- No --> Tetra_Voicemail[Leave Voicemail<br>Log Callback Attempt]:::tetra
    Tetra_Voicemail --> End_Callback([Callback Logged]):::terminator

    %% =============================================
    %% STEP 2: RETURN VISITOR CHECK - WEB
    %% =============================================
    HO_Entry --> Dec_Already_Scheduled{Already<br>Scheduled?}:::logic
    
    %% --- ALREADY SCHEDULED - WEB ---
    Dec_Already_Scheduled -- Yes --> Show_Status_Web[Show: You have a visit scheduled<br>Date, Time, CO Info]:::system
    Show_Status_Web --> Dec_Want_Resched_Web{Want to<br>Reschedule?}:::logic
    Dec_Want_Resched_Web -- Yes --> HO_Cancel
    Dec_Want_Resched_Web -- No --> End_Status_Web([Session End]):::terminator
    
    Dec_Already_Scheduled -- No --> Dec_Existing

    %% =============================================
    %% STEP 3: EXISTING CUSTOMER CHECK
    %% =============================================
    Dec_Existing{Existing<br>Customer?}:::logic

    %% --- NEW CUSTOMER PATH ---
    Dec_Existing -- No --> Sys_Check{High Confidence<br>in HVAC Details?}:::logic

    %% --- EXISTING CUSTOMER PATH ---
    Dec_Existing -- Yes --> Dec_FullyCovered{On Fully<br>Covered Plan?}:::logic

    Dec_FullyCovered -- Yes --> Flag_NoPay[Flag: No Payment Required]:::system
    Flag_NoPay --> Sys_Check_Existing

    Dec_FullyCovered -- No --> Dec_Warranty{Within 1-Year<br>Labor Warranty?}:::logic
    Dec_Warranty -- Yes --> Flag_LaborWarranty[Flag: Labor Covered<br>Parts may apply]:::system
    Flag_LaborWarranty --> Sys_Check_Existing

    Dec_Warranty -- No --> Dec_PartsWarranty{Within 5-10 Year<br>Parts Warranty?}:::logic
    Dec_PartsWarranty -- Yes --> Flag_PartsWarranty[Flag: Parts Covered<br>Labor billable]:::system
    Flag_PartsWarranty --> Sys_Check_Existing
    Dec_PartsWarranty -- No --> Sys_Check_Existing

    Sys_Check_Existing{High Confidence<br>in HVAC Details?}:::logic

    %% =============================================
    %% STEP 4: EQUIPMENT IDENTIFICATION
    %% =============================================
    Sys_Check -- Yes --> Bot_Triage
    Sys_Check_Existing -- Yes --> Bot_Triage

    Sys_Check -- No --> Bot_Ask[What heating system<br>do you have?]:::system
    Sys_Check_Existing -- No --> Bot_Ask
    Bot_Ask --> HO_Know{User Knows?}:::logic

    HO_Know -- No --> Bot_Guide[Guide user to answer]:::system
    Bot_Guide --> HO_Input

    HO_Know -- Yes --> HO_Input[Select Heating Type<br>Fuel and AC Status]:::user
    HO_Input --> Sys_Twin[Generate Digital Twin]:::system
    Sys_Twin --> Bot_Triage

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

    %% =============================================
    %% STEP 6: EMERGENCY vs AFTER-HOURS CHECK
    %% =============================================
    Dec_Sched -- Yes --> Dec_Emergency{Emergency<br>Visit Needed?}:::logic

    Dec_Emergency -- Yes --> Flag_Emergency[Flag: Emergency Visit<br>Premium Pricing]:::alert
    Flag_Emergency --> Sys_Cal

    Dec_Emergency -- No --> Dec_AfterHours{After-Hours<br>Visit?}:::logic
    Dec_AfterHours -- Yes --> Flag_AfterHours[Flag: After-Hours<br>Additional Charge]:::system
    Flag_AfterHours --> Sys_Cal
    Dec_AfterHours -- No --> Sys_Cal

    Sys_Cal[Display Calendar]:::system

    %% =============================================
    %% STEP 7: SCHEDULING
    %% =============================================
    Sys_Cal --> HO_Slot[Select Time Slot]:::user
    HO_Slot --> HO_Contact[Input Contact Info<br>Name, Email, Phone]:::user

    %% =============================================
    %% STEP 8: BOOKING CONFIRMATION
    %% =============================================
    HO_Contact --> HO_Book[Confirm Booking]:::user
    HO_Book --> Sys_Notif[Send Confirmation<br>Email and Text]:::system
    Sys_Notif --> Confirm_Loop

    %% =============================================
    %% STEP 9: CONFIRMATION WINDOW
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

    %% --- CO RESCHEDULE PATH (System-driven) ---
    Dec_Resched -- CO Reschedules --> Sys_Pull_Avail[Pull CO Availability<br>from ServiceTitan]:::system
    Sys_Pull_Avail --> Sys_Notify_HO[Notify HO: Time Change<br>Offer alternative slots]:::system
    Sys_Notify_HO --> Dec_HO_Accept{HO Accepts<br>New Time?}:::logic
    Dec_HO_Accept -- Yes --> Confirm_Loop
    Dec_HO_Accept -- No --> Proc_Reengage

    %% =============================================
    %% STEP 10: ARRIVAL & NO-SHOW CHECK
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
    %% STEP 11: DIAGNOSIS
    %% =============================================
    CO_Diagnose[CO Diagnoses Issue]:::co
    CO_Diagnose --> Sys_GenScope[System Generates<br>Recommended Fix]:::system

    %% =============================================
    %% STEP 12: PAYMENT FLAG CHECK (Before Line Items Review)
    %% =============================================
    Sys_GenScope --> Dec_Payment_Flag{Payment<br>Flag Set?}:::logic

    Dec_Payment_Flag -- Fully Covered --> Show_NoPay[Show: No Payment<br>Plan Benefit]:::system
    Show_NoPay --> CO_Review_Lines

    Dec_Payment_Flag -- Labor Warranty --> Show_PartsOnly[Show: Parts Only<br>Labor Covered]:::system
    Show_PartsOnly --> CO_Review_Lines

    Dec_Payment_Flag -- Parts Warranty --> Show_LaborOnly[Show: Labor Only<br>Parts Covered]:::system
    Show_LaborOnly --> CO_Review_Lines

    Dec_Payment_Flag -- No Flag --> Show_FullPrice[Show: Full Price]:::system
    Show_FullPrice --> CO_Review_Lines

    %% =============================================
    %% STEP 13: CO REVIEWS LINE ITEMS (After Payment Flag)
    %% =============================================
    CO_Review_Lines[CO Reviews and Edits<br>Line Items with HO]:::co
    CO_Review_Lines --> Dec_Repair{Repair Decision?}:::logic

    %% =============================================
    %% STEP 14: REPAIR DECISION
    %% =============================================

    %% Path A: No Repair - Diagnostic Fee Only
    Dec_Repair -- Decline --> HO_Pay_Diag[Pay Diagnostic Fee]:::user
    HO_Pay_Diag --> Sys_Pay_Diag[Process Diagnostic Fee]:::system
    Sys_Pay_Diag --> End_DiagOnly([Diagnostic Complete]):::terminator

    %% Path B: Approve Repair
    Dec_Repair -- Approve --> HO_Approve[Approve Scope<br>and Payment]:::user

    %% Path C: Replace - Sales Process
    Dec_Repair -- Replace --> Proc_Sales[[Sales Process]]:::subprocess
    Proc_Sales --> Sales_Consult[Schedule Sales<br>Consultation]:::system
    Sales_Consult --> Sales_Quote[Generate<br>Replacement Quote]:::system
    Sales_Quote --> Dec_Sales{HO Approves<br>Replacement?}:::logic
    Dec_Sales -- Yes --> End_Ecom([Ecom/Install Flow]):::terminator
    Dec_Sales -- No --> End_SalesLost([Sales Lost]):::terminator

    %% =============================================
    %% STEP 15: PARTS CHECK
    %% =============================================
    HO_Approve --> Dec_Parts{Parts Needed?}:::logic

    Dec_Parts -- No --> Dec_SameDay{Same-Day<br>Repair Possible?}:::logic

    Dec_Parts -- Yes --> Sys_Order[Tetra Orders Parts]:::tetra
    Sys_Order --> Sys_Parts_ETA[Notify HO: Parts ETA]:::system
    Sys_Parts_ETA --> Parts_Arrive[Parts Arrive]:::system
    Parts_Arrive --> Sched_Followup

    %% =============================================
    %% STEP 16: SAME-DAY REPAIR CHECK
    %% =============================================
    Dec_SameDay -- Yes --> CO_Execute

    %% --- REPAIR CANT BE DONE TODAY ---
    Dec_SameDay -- No --> Sys_Explain[Explain: Repair requires<br>additional time or resources]:::system
    Sys_Explain --> Sched_Followup

    %% --- SCHEDULE FOLLOW-UP ---
    Sched_Followup[[Schedule Follow-Up Visit]]:::subprocess
    Sched_Followup --> HO_FollowSlot[Select Follow-Up Time]:::user
    HO_FollowSlot --> Confirm_Loop

    %% =============================================
    %% STEP 17: EXECUTION & COMPLETION
    %% =============================================
    CO_Execute[CO Performs Repair]:::co
    CO_Execute --> HO_Sign[Sign Off on Work]:::user

    %% =============================================
    %% STEP 18: PAYMENT COLLECTION (After Repair)
    %% =============================================
    HO_Sign --> HO_Pay{HO Pays?}:::logic

    HO_Pay -- Yes --> Sys_Pay[Process Payment]:::system
    Sys_Pay --> Sys_Report[Send Report to HO]:::system
    Sys_Report --> End_Journey([Journey Complete]):::terminator

    %% --- NON-PAYMENT PATH ---
    HO_Pay -- No --> Tetra_Collection[Tetra Reaches Out<br>for Payment Collection]:::tetra
    Tetra_Collection --> Dec_Collection{Payment<br>Collected?}:::logic
    Dec_Collection -- Yes --> Sys_Pay
    Dec_Collection -- No --> Flag_Unpaid[Flag: Unpaid<br>Send to Collections]:::system
    Flag_Unpaid --> End_Unpaid([Unpaid - Collections]):::terminator