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
    classDef chargeback fill:#ffebee,stroke:#c62828,color:#b71c1c,stroke-width:2px

    %% =============================================
    %% STEP 1: JOB ASSIGNMENT
    %% =============================================
    StartNode([HO Books Visit]) --> Sys_ST[Job Created in ServiceTitan]:::system
    Sys_ST --> Sys_Flags[Job Includes Customer Flags:<br>Existing Customer Status<br>Warranty Status<br>Emergency/After-Hours Flag]:::system
    Sys_Flags --> CO_Cal[Appointment Appears<br>on CO Calendar]:::co

    %% =============================================
    %% STEP 2: EMERGENCY vs AFTER-HOURS HANDLING
    %% =============================================
    CO_Cal --> Dec_Emergency{Emergency<br>Flag Set?}:::logic

    Dec_Emergency -- Yes --> Tetra_Call_CO_Emerg[Tetra Calls CO:<br>Alert Emergency Visit<br>Confirm Availability]:::tetra
    Tetra_Call_CO_Emerg --> Dec_CO_Avail_Emerg{CO<br>Available?}:::logic
    Dec_CO_Avail_Emerg -- Yes --> CO_Priority_Emerg[CO Prioritizes Job<br>Emergency Premium Rate]:::co
    Dec_CO_Avail_Emerg -- No --> Sys_Reassign_Emerg[Reassign to<br>Available CO]:::system
    Sys_Reassign_Emerg --> CO_Priority_Emerg
    CO_Priority_Emerg --> CO_Review_Handoff

    Dec_Emergency -- No --> Dec_AfterHours{After-Hours<br>Flag Set?}:::logic
    
    Dec_AfterHours -- Yes --> Tetra_Call_CO_AH[Tetra Calls CO:<br>Alert After-Hours Visit<br>Confirm Availability]:::tetra
    Tetra_Call_CO_AH --> Dec_CO_Avail_AH{CO<br>Available?}:::logic
    Dec_CO_Avail_AH -- Yes --> CO_Priority_AH[CO Accepts Job<br>After-Hours Surcharge]:::co
    Dec_CO_Avail_AH -- No --> Sys_Reassign_AH[Reassign to<br>Available CO]:::system
    Sys_Reassign_AH --> CO_Priority_AH
    CO_Priority_AH --> CO_Review_Handoff

    Dec_AfterHours -- No --> CO_Review_Handoff

    %% =============================================
    %% STEP 3: HANDOFF DOCUMENT REVIEW
    %% =============================================
    CO_Review_Handoff[CO Reviews<br>Handoff Document]:::co

    %% =============================================
    %% CONTRACTOR SERVICE DECISION
    %% =============================================
    CO_Review_Handoff --> Dec_WillService{CO Will Service<br>Customer?}:::logic

    %% --- DEFAULT PATH: CO SERVICES THE CUSTOMER ---
    Dec_WillService -- "Yes - Default" --> Dec_Has_Parts

    %% --- ALTERNATE PATH: CO DECLINES TO SERVICE ---
    Dec_WillService -- No --> CO_Decline_Reason[CO Logs Reason:<br>Unavailable or Unwilling]:::co
    CO_Decline_Reason --> Sys_Find_Alt[Find Alternative<br>Servicing Contractor]:::system
    Sys_Find_Alt --> Tetra_Call_Alt[Tetra Calls<br>Alt Contractor]:::tetra
    Tetra_Call_Alt --> Dec_Alt_Avail{Alt CO<br>Available?}:::logic
    Dec_Alt_Avail -- No --> Sys_Find_Alt
    Dec_Alt_Avail -- Yes --> Alt_CO_Accept[Alt CO Accepts Job]:::co
    Alt_CO_Accept --> Flag_Chargeback[Flag: Chargeback<br>Original Installer]:::chargeback
    Flag_Chargeback --> Dec_Has_Parts

    %% =============================================
    %% STEP 4: PARTS CHECK AND CONFIRMATION
    %% =============================================
    Dec_Has_Parts{Has Required<br>Parts/Equipment?}:::logic
    Dec_Has_Parts -- Yes --> Confirm_Loop
    Dec_Has_Parts -- No --> CO_Get_Parts[CO Gets Required<br>Parts/Equipment]:::co
    CO_Get_Parts --> Confirm_Loop

    Confirm_Loop[[Confirmation Loop]]:::subprocess
    Confirm_Loop --> Dec_Resched{Reschedule?}:::logic

    Dec_Resched -- No --> CO_Arrive

    %% --- CO RESCHEDULE PATH ---
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
    %% STEP 5: ARRIVAL AND NO-SHOW CHECK
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
    CO_Diagnose[Diagnose Issue<br>QandA, Photos, Readings]:::co
    CO_Diagnose --> Sys_Gen[Generate Recommended<br>Fix and Line Items]:::system

    %% =============================================
    %% STEP 7: PAYMENT FLAG CHECK
    %% =============================================
    Sys_Gen --> Dec_Payment_Flag{Payment<br>Flag Set?}:::logic

    Dec_Payment_Flag -- Fully Covered --> Show_NoPay[Show Pricing:<br>No Payment Required<br>Plan Benefit]:::system
    Dec_Payment_Flag -- Labor Warranty --> Show_PartsOnly[Show Pricing:<br>Parts Only<br>Labor Covered]:::system
    Dec_Payment_Flag -- Parts Warranty --> Show_LaborOnly[Show Pricing:<br>Labor Only<br>Parts Covered]:::system
    Dec_Payment_Flag -- No Flag --> Show_FullPrice[Show Pricing:<br>Full Price]:::system

    Show_NoPay --> CO_Review_Lines
    Show_PartsOnly --> CO_Review_Lines
    Show_LaborOnly --> CO_Review_Lines
    Show_FullPrice --> CO_Review_Lines

    %% =============================================
    %% STEP 8: CO REVIEWS LINE ITEMS WITH HO
    %% =============================================
    CO_Review_Lines[CO Reviews and Edits<br>Line Items with HO]:::co
    CO_Review_Lines --> Dec_Repair{HO Decision?}:::logic

    %% =============================================
    %% STEP 9: REPAIR DECISION
    %% =============================================

    %% Path A: No Repair - Diagnostic Fee Only
    Dec_Repair -- Decline Repair --> CO_Collect_Diag[Collect Diagnostic Fee]:::co
    CO_Collect_Diag --> Dec_Diag_Paid{HO Pays<br>Diagnostic?}:::logic
    Dec_Diag_Paid -- Yes --> Sys_Diag_Pay[Process Diagnostic Fee]:::system
    Sys_Diag_Pay --> Check_Chargeback_Diag{Chargeback<br>Flagged?}:::logic
    Check_Chargeback_Diag -- Yes --> Process_CB_Diag[Process Chargeback:<br>Original Installer]:::chargeback
    Process_CB_Diag --> End_DiagOnly([Diagnostic Complete]):::terminator
    Check_Chargeback_Diag -- No --> End_DiagOnly
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
    Dec_Repair -- Repair and Replace --> CO_Set_Lead_Both[CO Sets Replacement Lead]:::co
    CO_Set_Lead_Both --> Sys_CO_Commission_Both[Pay CO Lead Commission]:::system
    Sys_CO_Commission_Both --> HO_Approve

    %% =============================================
    %% STEP 10: PARTS CHECK
    %% =============================================
    Dec_Parts -- No --> Dec_SameDay{Same-Day<br>Repair Possible?}:::logic

    Dec_Parts -- Yes --> Alert_Tetra[Alert Tetra:<br>Parts Needed]:::tetra
    Alert_Tetra --> Tetra_Order[Tetra Orders Parts]:::tetra
    Tetra_Order --> Sys_Parts_ETA[Notify HO and CO:<br>Parts ETA]:::system
    Sys_Parts_ETA --> Parts_Arrive[Parts Arrive<br>at CO Warehouse]:::system
    Parts_Arrive --> Sched_Loop

    %% =============================================
    %% STEP 11: SAME-DAY REPAIR CHECK
    %% =============================================
    Dec_SameDay -- Yes --> CO_Execute

    Dec_SameDay -- No --> CO_Explain[Explain to HO:<br>Repair requires additional<br>time or resources]:::co
    CO_Explain --> Sched_Loop

    %% --- SCHEDULE FOLLOW-UP ---
    Sched_Loop[[Schedule Follow-Up]]:::subprocess
    Sched_Loop --> HO_FollowSlot[HO Selects Follow-Up Time]:::user
    HO_FollowSlot --> Sys_ST_Followup[Create Follow-Up<br>in ServiceTitan]:::system
    Sys_ST_Followup --> Confirm_Loop

    %% =============================================
    %% STEP 12: EXECUTION
    %% =============================================
    CO_Execute[Perform Repair Work]:::co
    CO_Execute --> CO_Photo[Take Verification Photos]:::co
    CO_Photo --> CO_Complete[Mark Complete in App]:::co

    %% =============================================
    %% STEP 13: COMPLETION AND PAYMENT
    %% =============================================
    CO_Complete --> HO_Sign[HO Signs Off]:::user
    HO_Sign --> CO_Collect_Payment[CO Collects Payment]:::co
    CO_Collect_Payment --> Dec_HO_Pays{HO Pays?}:::logic

    %% --- PAYMENT COLLECTED ---
    Dec_HO_Pays -- Yes --> Sys_Pay[Process Payment]:::system
    Sys_Pay --> Check_Chargeback{Chargeback<br>Flagged?}:::logic
    Check_Chargeback -- Yes --> Process_Chargeback[Process Chargeback:<br>Original Installer]:::chargeback
    Process_Chargeback --> Dec_Still_Active{Original Installer<br>Still Active?}:::logic
    Dec_Still_Active -- Yes --> Deduct_Invoice[Deduct Service Call +<br>Repair from Next Invoice]:::chargeback
    Dec_Still_Active -- No --> Flag_Debt[Flag: Outstanding Debt<br>from Former Contractor]:::chargeback
    Deduct_Invoice --> Sys_Report
    Flag_Debt --> Sys_Report
    Check_Chargeback -- No --> Sys_Report
    Sys_Report[Send Report<br>to HO and CO]:::system
    Sys_Report --> CO_Paid[CO Receives Payment]:::co
    CO_Paid --> End_Journey([Journey Complete]):::terminator

    %% --- PAYMENT NOT COLLECTED ---
    Dec_HO_Pays -- No --> CO_Log_NoPay[CO Logs: Payment<br>Not Collected]:::co
    CO_Log_NoPay --> CO_No_Pay_Yet[CO Does Not<br>Receive Payment Yet]:::co
    CO_No_Pay_Yet --> Tetra_Collection[Tetra Reaches Out<br>for Payment Collection]:::tetra
    Tetra_Collection --> Dec_Collection{Payment<br>Collected?}:::logic
    Dec_Collection -- Yes --> Sys_Pay_Late[Process Late Payment]:::system
    Sys_Pay_Late --> Check_Chargeback_Late{Chargeback<br>Flagged?}:::logic
    Check_Chargeback_Late -- Yes --> Process_CB_Late[Process Chargeback:<br>Original Installer]:::chargeback
    Process_CB_Late --> Dec_Active_Late{Original Installer<br>Still Active?}:::logic
    Dec_Active_Late -- Yes --> Deduct_Late[Deduct Service Call +<br>Repair from Next Invoice]:::chargeback
    Dec_Active_Late -- No --> Flag_Debt_Late[Flag: Outstanding Debt]:::chargeback
    Deduct_Late --> CO_Paid_Late
    Flag_Debt_Late --> CO_Paid_Late
    Check_Chargeback_Late -- No --> CO_Paid_Late
    CO_Paid_Late[CO Receives Payment]:::co
    CO_Paid_Late --> End_Journey_Late([Journey Complete - Late Pay]):::terminator
    Dec_Collection -- No --> Flag_Unpaid[Flag: Unpaid<br>Send to Collections]:::system
    Flag_Unpaid --> End_Unpaid([Unpaid - Collections]):::terminator