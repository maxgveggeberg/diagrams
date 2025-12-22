```mermaid

flowchart TD
    %% --- STYLING ---
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef co fill:#fff8e1,stroke:#ffa000,color:#5d4037,stroke-width:2px
    classDef tetra fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20,stroke-width:2px
    classDef system fill:#f5f5f5,stroke:#333333,color:#333333,stroke-width:1px,stroke-dasharray: 5 5
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef alert fill:#fff0e6,stroke:#d9534f,color:#d9534f,stroke-width:2px
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef subprocess fill:#f4f4f4,stroke:#333333,stroke-width:2px
    classDef monitor fill:#e3f2fd,stroke:#1565c0,color:#0d47a1,stroke-width:2px
    classDef chargeback fill:#ffebee,stroke:#c62828,color:#b71c1c,stroke-width:2px

    %% =============================================
    %% PHASE 1: INTAKE
    %% =============================================
    subgraph Phase1[PHASE 1: INTAKE]
        direction TB
        
        %% Entry Points
        Start_Web([HO Lands on RepairBot]):::user
        Start_Phone([HO Calls In]):::user
        
        Start_Web --> HO_Entry_Web[HO Enters Address<br>and Describes Issue]:::user
        
        %% --- PHONE PATH WITH CONTACT COLLECTION ---
        Start_Phone --> Dec_Answer{Call<br>Answered?}:::logic
        Dec_Answer -- Yes --> CSR_Answer[CSR Answers Call]:::tetra
        CSR_Answer --> CSR_Collect[CSR Collects Contact Info:<br>Phone, Address]:::tetra
        
        %% --- ALREADY SCHEDULED CHECK - PHONE ---
        CSR_Collect --> Dec_Sched_Phone{Already<br>Scheduled?}:::logic
        Dec_Sched_Phone -- Yes --> Show_Status_Phone[Show Booking Status<br>Offer Reschedule]:::tetra
        Show_Status_Phone --> Dec_Resched_Phone{Want to<br>Reschedule?}:::logic
        Dec_Resched_Phone -- Yes --> HO_Cancel
        Dec_Resched_Phone -- No --> End_Status_Phone([Call Complete]):::terminator
        Dec_Sched_Phone -- No --> CSR_Entry[CSR Enters Issue]:::tetra
        
        %% --- CALLBACK FLOW ---
        Dec_Answer -- No --> Tetra_Callback[Tetra Calls Back]:::tetra
        Tetra_Callback --> Dec_Callback{Answers?}:::logic
        Dec_Callback -- Yes --> CSR_Answer
        Dec_Callback -- No --> Tetra_Voicemail[Leave Voicemail]:::tetra
        Tetra_Voicemail --> End_Callback([Logged]):::terminator
        
        %% --- ALREADY SCHEDULED CHECK - WEB ---
        HO_Entry_Web --> Dec_Sched_Web{Already<br>Scheduled?}:::logic
        Dec_Sched_Web -- Yes --> Show_Status_Web[Show: Visit Scheduled<br>Offer Reschedule]:::system
        Show_Status_Web --> Dec_Resched_Web{Want to<br>Reschedule?}:::logic
        Dec_Resched_Web -- Yes --> HO_Cancel
        Dec_Resched_Web -- No --> End_Status_Web([Session End]):::terminator
        Dec_Sched_Web -- No --> Dec_Existing
        
        CSR_Entry --> Dec_Existing
        
        %% Customer Check
        Dec_Existing{Existing<br>Customer?}:::logic
        
        Dec_Existing -- Yes --> Sys_PullFlags[Pull Flags:<br>Plan, Warranty, History]:::system
        Sys_PullFlags --> Dec_FullyCovered{Fully<br>Covered?}:::logic
        
        Dec_FullyCovered -- Yes --> Flag_NoPay[Flag: No Payment]:::system
        Dec_FullyCovered -- No --> Dec_Warranty{Labor<br>Warranty?}:::logic
        Dec_Warranty -- Yes --> Flag_Labor[Flag: Labor Covered]:::system
        Dec_Warranty -- No --> Dec_PartsWarranty{Parts<br>Warranty?}:::logic
        Dec_PartsWarranty -- Yes --> Flag_Parts[Flag: Parts Covered]:::system
        Dec_PartsWarranty -- No --> Flag_Standard[Flag: Standard]:::system
        
        Flag_NoPay --> Dec_Confidence
        Flag_Labor --> Dec_Confidence
        Flag_Parts --> Dec_Confidence
        Flag_Standard --> Dec_Confidence
        
        Dec_Existing -- No --> Flag_New[Flag: New Customer]:::system
        Flag_New --> Dec_Confidence
        
        %% Equipment Confidence
        Dec_Confidence{High Confidence<br>in HVAC?}:::logic
        Dec_Confidence -- Yes --> Bot_Triage
        Dec_Confidence -- No --> Guide_Equip[Guide Equipment ID]:::system
        Guide_Equip --> Sys_Twin[Update Digital Twin]:::system
        Sys_Twin --> Bot_Triage
        
        Bot_Triage[Triage Symptoms]:::system
    end

    %% =============================================
    %% PHASE 2: SCHEDULING
    %% =============================================
    subgraph Phase2[PHASE 2: SCHEDULING]
        direction TB
        
        Bot_Triage --> Dec_Schedule{Wants to<br>Schedule?}:::logic
        
        Dec_Schedule -- No --> Offer_Report[Offer Chat Report]:::system
        Offer_Report --> Dec_Report{Wants<br>Report?}:::logic
        Dec_Report -- Yes --> End_Lead([Lead Captured]):::terminator
        Dec_Report -- No --> End_Exit([Exit]):::terminator
        
        %% --- EMERGENCY vs AFTER-HOURS ---
        Dec_Schedule -- Yes --> Dec_Emergency{Emergency?}:::logic
        Dec_Emergency -- Yes --> Flag_Emergency[Flag: Emergency<br>Premium Rate]:::alert
        Flag_Emergency --> Show_Calendar
        
        Dec_Emergency -- No --> Dec_AfterHours{After-Hours?}:::logic
        Dec_AfterHours -- Yes --> Flag_AfterHours[Flag: After-Hours<br>Surcharge]:::system
        Flag_AfterHours --> Show_Calendar
        Dec_AfterHours -- No --> Show_Calendar
        
        Show_Calendar[Display Calendar]:::system
        Show_Calendar --> HO_Select[HO Selects Time]:::user
        HO_Select --> HO_Contact[Confirm Contact Info]:::user
        HO_Contact --> HO_Book[Confirm Booking]:::user
        HO_Book --> Sys_Create[Create Job]:::system
        Sys_Create --> Sys_Assign[Assign CO]:::system
        
        %% --- EMERGENCY/AFTER-HOURS CO CALL ---
        Sys_Assign --> Dec_UrgentFlag{Emergency or<br>After-Hours?}:::logic
        Dec_UrgentFlag -- Emergency --> Tetra_Call_Emerg[Tetra Calls CO:<br>Emergency Alert]:::tetra
        Tetra_Call_Emerg --> Dec_CO_E{Available?}:::logic
        Dec_CO_E -- Yes --> Sys_Notify
        Dec_CO_E -- No --> Sys_Reassign_E[Reassign CO]:::system
        Sys_Reassign_E --> Sys_Notify
        
        Dec_UrgentFlag -- After-Hours --> Tetra_Call_AH[Tetra Calls CO:<br>After-Hours Alert]:::tetra
        Tetra_Call_AH --> Dec_CO_AH{Available?}:::logic
        Dec_CO_AH -- Yes --> Sys_Notify
        Dec_CO_AH -- No --> Sys_Reassign_AH[Reassign CO]:::system
        Sys_Reassign_AH --> Sys_Notify
        
        Dec_UrgentFlag -- No --> Sys_Notify
        
        Sys_Notify[Send Confirmations]:::system
        Sys_Notify --> CO_Receive[CO Receives Job]:::co
        CO_Receive --> CO_Handoff[CO Reviews Handoff Doc]:::co
        
        %% =============================================
        %% CONTRACTOR SERVICE DECISION
        %% =============================================
        CO_Handoff --> Dec_WillService{CO Will Service<br>Customer?}:::logic
        
        %% --- DEFAULT PATH: CO SERVICES ---
        Dec_WillService -- "Yes - Default" --> Dec_Parts_Ready
        
        %% --- ALTERNATE PATH: CO DECLINES ---
        Dec_WillService -- No --> CO_Decline[CO Logs Reason:<br>Unavailable or Unwilling]:::co
        CO_Decline --> Find_Alt_CO[Find Alternative<br>Servicing Contractor]:::system
        Find_Alt_CO --> Tetra_Call_Alt[Tetra Calls<br>Alt Contractor]:::tetra
        Tetra_Call_Alt --> Dec_Alt_Avail{Alt CO<br>Available?}:::logic
        Dec_Alt_Avail -- No --> Find_Alt_CO
        Dec_Alt_Avail -- Yes --> Alt_Accept[Alt CO Accepts Job]:::co
        Alt_Accept --> Flag_Chargeback[Flag: Chargeback<br>Original Installer]:::chargeback
        Flag_Chargeback --> Dec_Parts_Ready
        
        Dec_Parts_Ready{Has Parts?}:::logic
        Dec_Parts_Ready -- Yes --> Confirm_Window
        Dec_Parts_Ready -- No --> CO_Get_Parts[CO Gets Parts]:::co
        CO_Get_Parts --> Confirm_Window
    end

    %% =============================================
    %% PHASE 3: CONFIRMATION
    %% =============================================
    subgraph Phase3[PHASE 3: CONFIRMATION]
        direction TB
        
        Confirm_Window[[Confirmation Window]]:::subprocess
        Confirm_Window --> Sys_Remind[Send Reminders]:::system
        Sys_Remind --> Dec_Resched{Reschedule?}:::logic
        
        Dec_Resched -- No --> Visit_Confirmed([Confirmed])
        
        Dec_Resched -- HO Reschedules --> HO_Cancel[HO Cancels Slot]:::user
        HO_Cancel --> Dec_Immediate{Immediate<br>Reschedule?}:::logic
        Dec_Immediate -- Yes --> HO_NewSlot[Select New Time]:::user
        HO_NewSlot --> Sys_Update[Update Job]:::system
        Sys_Update --> Confirm_Window
        Dec_Immediate -- No --> Reengage
        
        Dec_Resched -- CO Reschedules --> Sys_Pull[Pull Availability]:::system
        Sys_Pull --> Sys_NotifyHO[Notify HO Options]:::system
        Sys_NotifyHO --> Dec_Accept{Accepts?}:::logic
        Dec_Accept -- Yes --> Sys_Update
        Dec_Accept -- No --> Reengage
        
        Reengage[[Re-engagement]]:::subprocess
        Reengage --> Tetra_Outreach[Tetra Outreach]:::tetra
        Tetra_Outreach --> Dec_Respond{Responds?}:::logic
        Dec_Respond -- Yes --> HO_NewSlot
        Dec_Respond -- No --> Mark_Cold[Mark Cold]:::tetra
        Mark_Cold --> End_Cancel([Cancelled]):::terminator
    end

    %% =============================================
    %% PHASE 4: VISIT DAY
    %% =============================================
    subgraph Phase4[PHASE 4: VISIT DAY]
        direction TB
        
        Visit_Confirmed --> Monitor_Route[Monitor: En Route]:::monitor
        Monitor_Route --> CO_Arrive[CO Arrives]:::co
        CO_Arrive --> CO_Checkin[CO Checks In]:::co
        CO_Checkin --> Dec_Home{HO Home?}:::logic
        
        Dec_Home -- No --> CO_Wait[CO Waits]:::co
        CO_Wait --> Dec_NoShow{Responds?}:::logic
        Dec_NoShow -- No --> Log_NoShow[Log No-Show]:::system
        Log_NoShow --> Reengage
        Dec_NoShow -- Yes --> CO_Diagnose
        
        Dec_Home -- Yes --> CO_Diagnose
    end

    %% =============================================
    %% PHASE 5: DIAGNOSIS AND SCOPE
    %% =============================================
    subgraph Phase5[PHASE 5: DIAGNOSIS]
        direction TB
        
        CO_Diagnose[CO Diagnoses]:::co
        CO_Diagnose --> Sys_Scope[Generate Scope]:::system
        
        %% --- PAYMENT FLAG BEFORE LINE ITEMS REVIEW ---
        Sys_Scope --> Dec_PayFlag{Payment<br>Flag?}:::logic
        Dec_PayFlag -- Fully Covered --> Show_NoPay[Show: No Payment]:::system
        Dec_PayFlag -- Labor Warranty --> Show_Parts[Show: Parts Only]:::system
        Dec_PayFlag -- Parts Warranty --> Show_Labor[Show: Labor Only]:::system
        Dec_PayFlag -- Standard --> Show_Full[Show: Full Price]:::system
        
        Show_NoPay --> CO_Review_Lines
        Show_Parts --> CO_Review_Lines
        Show_Labor --> CO_Review_Lines
        Show_Full --> CO_Review_Lines
        
        %% --- CO REVIEWS LINE ITEMS WITH HO ---
        CO_Review_Lines[CO Reviews and Edits<br>Line Items with HO]:::co
        CO_Review_Lines --> Dec_Decision{Decision?}:::logic
        
        Dec_Decision -- Decline --> Collect_Diag[Collect Diagnostic]:::co
        Collect_Diag --> Check_CB_Diag{Chargeback<br>Flagged?}:::logic
        Check_CB_Diag -- Yes --> Process_CB_Diag[Process Chargeback:<br>Original Installer]:::chargeback
        Process_CB_Diag --> End_Diag([Diagnostic Done]):::terminator
        Check_CB_Diag -- No --> End_Diag
        
        Dec_Decision -- Replace --> CO_Lead[CO Sets Lead]:::co
        CO_Lead --> Pay_Commission[Pay CO Commission]:::system
        Pay_Commission --> Sales[[Sales Process]]:::subprocess
        Sales --> End_Ecom([Ecom Flow]):::terminator
        
        Dec_Decision -- Repair and Replace --> CO_Lead_Both[CO Sets Lead]:::co
        CO_Lead_Both --> Pay_Comm_Both[Pay CO Commission]:::system
        Pay_Comm_Both --> HO_Approve
        
        Dec_Decision -- Approve --> HO_Approve[HO Approves]:::user
    end

    %% =============================================
    %% PHASE 6: PARTS AND FOLLOW-UP
    %% =============================================
    subgraph Phase6[PHASE 6: PARTS]
        direction TB
        
        HO_Approve --> Dec_Parts{Parts?}:::logic
        Dec_Parts -- No --> Dec_SameDay{Same Day?}:::logic
        Dec_SameDay -- Yes --> CO_Execute
        Dec_SameDay -- No --> Explain[Explain Delay]:::system
        Explain --> Sched_Follow
        
        Dec_Parts -- Yes --> Alert_Parts[Alert Tetra]:::tetra
        Alert_Parts --> Order_Parts[Order Parts]:::tetra
        Order_Parts --> Track_Parts[Track Shipment]:::tetra
        Track_Parts --> Parts_Ready[Parts Arrive]:::system
        Parts_Ready --> Notify_CO[Notify CO]:::system
        Notify_CO --> Sched_Follow
        
        Sched_Follow[[Schedule Follow-Up]]:::subprocess
        Sched_Follow --> HO_Follow[HO Selects Time]:::user
        HO_Follow --> Sys_Follow[Create in ServiceTitan]:::system
        Sys_Follow --> Confirm_Window
    end

    %% =============================================
    %% PHASE 7: EXECUTION
    %% =============================================
    subgraph Phase7[PHASE 7: EXECUTION]
        direction TB
        
        CO_Execute[CO Performs Repair]:::co
        CO_Execute --> CO_Photos[Take Photos]:::co
        CO_Photos --> CO_Complete[Mark Complete]:::co
        CO_Complete --> HO_Sign[HO Signs Off]:::user
    end

    %% =============================================
    %% PHASE 8: PAYMENT
    %% =============================================
    subgraph Phase8[PHASE 8: PAYMENT]
        direction TB
        
        HO_Sign --> CO_Collect[CO Collects Payment]:::co
        CO_Collect --> Dec_Paid{Paid?}:::logic
        
        Dec_Paid -- Yes --> Process_Pay[Process Payment]:::tetra
        Process_Pay --> Check_Chargeback{Chargeback<br>Flagged?}:::logic
        Check_Chargeback -- Yes --> Process_Chargeback[Process Chargeback:<br>Original Installer]:::chargeback
        Process_Chargeback --> Dec_Still_Active{Original Installer<br>Still Active?}:::logic
        Dec_Still_Active -- Yes --> Deduct_Invoice[Deduct Service Call +<br>Repair from Next Invoice]:::chargeback
        Dec_Still_Active -- No --> Flag_Debt[Flag: Outstanding Debt<br>from Former Contractor]:::chargeback
        Deduct_Invoice --> Send_Report
        Flag_Debt --> Send_Report
        Check_Chargeback -- No --> Send_Report
        Send_Report[Send Reports]:::system
        Send_Report --> Pay_CO[Pay CO]:::tetra
        Pay_CO --> Update_Records[Update Records]:::system
        Update_Records --> End_Complete([Complete]):::terminator
        
        Dec_Paid -- No --> Log_NoPay[Log: Not Paid]:::co
        Log_NoPay --> CO_Waits[CO Waits for Payment]:::co
        CO_Waits --> Collection[Tetra Collection]:::tetra
        Collection --> Dec_Collected{Collected?}:::logic
        Dec_Collected -- Yes --> Process_Late[Process Late]:::tetra
        Process_Late --> Check_CB_Late{Chargeback<br>Flagged?}:::logic
        Check_CB_Late -- Yes --> Process_CB_Late[Process Chargeback:<br>Original Installer]:::chargeback
        Process_CB_Late --> Dec_Active_Late{Original Installer<br>Still Active?}:::logic
        Dec_Active_Late -- Yes --> Deduct_Late[Deduct from Next Invoice]:::chargeback
        Dec_Active_Late -- No --> Flag_Debt_Late[Flag: Outstanding Debt]:::chargeback
        Deduct_Late --> Pay_CO_Late
        Flag_Debt_Late --> Pay_CO_Late
        Check_CB_Late -- No --> Pay_CO_Late
        Pay_CO_Late[Pay CO]:::tetra
        Pay_CO_Late --> End_Late([Complete - Late]):::terminator
        Dec_Collected -- No --> Flag_Unpaid[Send to Collections]:::tetra
        Flag_Unpaid --> End_Unpaid([Unpaid]):::terminator
    end