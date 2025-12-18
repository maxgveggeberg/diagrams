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

    %% =============================================
    %% PHASE 1: INTAKE & QUALIFICATION
    %% =============================================
    subgraph Phase1[PHASE 1: INTAKE AND QUALIFICATION]
        direction TB
        
        Start([HO Calls In]) --> Dec_Answer{Call<br>Answered?}:::logic
        
        Dec_Answer -- Yes --> CSR_Answer[CSR Answers Call<br>Opens CSR RepairBot]:::tetra
        
        %% --- CALLBACK FLOW FOR MISSED CALLS ---
        Dec_Answer -- No --> Tetra_Callback[Tetra Calls<br>Customer Back]:::tetra
        Tetra_Callback --> Dec_Callback_Answer{Customer<br>Answers?}:::logic
        Dec_Callback_Answer -- Yes --> CSR_Answer
        Dec_Callback_Answer -- No --> Tetra_Voicemail[Leave Voicemail<br>Log Callback Attempt]:::tetra
        Tetra_Voicemail --> End_Callback([Callback Logged]):::terminator
        
        CSR_Answer --> CSR_Entry[CSR Enters Address<br>and Issue Description<br>in RepairBot]:::tetra
        
        CSR_Entry --> Dec_Existing{Existing<br>Customer?}:::logic
        
        Dec_Existing -- Yes --> Sys_PullFlags[System Pulls Customer Flags:<br>Plan Status<br>Warranty Status<br>Service History]:::system
        Sys_PullFlags --> Dec_FullyCovered{Fully<br>Covered?}:::logic
        
        Dec_FullyCovered -- Yes --> Flag_NoPay[Set Flag:<br>No Payment Required]:::system
        Dec_FullyCovered -- No --> Dec_Warranty{Labor<br>Warranty?}:::logic
        Dec_Warranty -- Yes --> Flag_Labor[Set Flag:<br>Labor Covered]:::system
        Dec_Warranty -- No --> Dec_PartsWarranty{Parts<br>Warranty?}:::logic
        Dec_PartsWarranty -- Yes --> Flag_Parts[Set Flag:<br>Parts Covered]:::system
        Dec_PartsWarranty -- No --> Sys_StandardFlags[Set Flag:<br>Standard Billing]:::system
        
        Flag_NoPay --> Dec_Confidence
        Flag_Labor --> Dec_Confidence
        Flag_Parts --> Dec_Confidence
        Sys_StandardFlags --> Dec_Confidence
        
        Dec_Existing -- No --> Flag_NewCust[Flag: New Customer<br>Digital Twin Required]:::system
        Flag_NewCust --> Dec_Confidence
        
        %% --- HOME DETAILS CONFIDENCE CHECK ---
        Dec_Confidence{High Confidence<br>in HVAC Details?}:::logic
        
        Dec_Confidence -- Yes --> Bot_Triage
        
        Dec_Confidence -- No --> CSR_AskEquip[CSR Asks HO:<br>What heating system<br>do you have?]:::tetra
        CSR_AskEquip --> Dec_HOKnows{HO Knows<br>Equipment?}:::logic
        
        Dec_HOKnows -- Yes --> CSR_RecordEquip[CSR Records:<br>Heating Type<br>Fuel Type<br>AC Status]:::tetra
        Dec_HOKnows -- No --> CSR_Guide[CSR Guides HO:<br>Check unit label<br>Describe location<br>Describe appearance]:::tetra
        CSR_Guide --> CSR_RecordEquip
        
        CSR_RecordEquip --> Sys_UpdateTwin[System Updates<br>Digital Twin]:::system
        Sys_UpdateTwin --> Bot_Triage
        
        Bot_Triage[RepairBot: Triage Symptoms<br>and Safety Check]:::system
    end

    %% =============================================
    %% PHASE 2: SCHEDULING & ASSIGNMENT
    %% =============================================
    subgraph Phase2[PHASE 2: SCHEDULING AND ASSIGNMENT]
        direction TB
        
        Bot_Triage --> Dec_Schedule{HO Wants to<br>Schedule?}:::logic
        
        Dec_Schedule -- No --> CSR_OfferReport[CSR Offers to Send<br>Chat Report via Email]:::tetra
        CSR_OfferReport --> Dec_WantReport{HO Wants<br>Report?}:::logic
        Dec_WantReport -- Yes --> CSR_CollectContact[CSR Collects<br>HO Contact Info]:::tetra
        CSR_CollectContact --> Sys_SendReport[System Sends<br>Chat Report Email]:::system
        Sys_SendReport --> End_NoSchedule([Call End:<br>Lead Captured]):::terminator
        Dec_WantReport -- No --> End_Exit([Call End:<br>No Capture]):::terminator
        
        %% --- EMERGENCY CHECK AFTER SCHEDULE CLICK ---
        Dec_Schedule -- Yes --> Dec_Emergency{Emergency<br>Visit?}:::logic
        Dec_Emergency -- Yes --> CSR_ValidateEmerg[CSR Validates Emergency:<br>Review Issue Description<br>Confirm Urgency with HO]:::tetra
        CSR_ValidateEmerg --> Dec_RealEmerg{Genuine<br>Emergency?}:::logic
        Dec_RealEmerg -- Yes --> Flag_Emergency[Set Flag:<br>Emergency + Premium Rate]:::alert
        Dec_RealEmerg -- No --> CSR_Downgrade[CSR Informs HO:<br>Standard Pricing Applies]:::tetra
        CSR_Downgrade --> CSR_ShowCal
        Flag_Emergency --> CSR_ShowCal
        Dec_Emergency -- No --> CSR_ShowCal
        
        CSR_ShowCal[CSR Reviews Available<br>Time Slots with HO]:::tetra
        CSR_ShowCal --> HO_SelectSlot[HO Selects Time Slot]:::user
        HO_SelectSlot --> CSR_ConfirmContact[CSR Confirms Contact Info:<br>Name, Phone, Email]:::tetra
        CSR_ConfirmContact --> Sys_CreateJob[System Creates Job<br>in ServiceTitan:<br>Attach All Flags<br>Set Priority Level]:::system
        
        Sys_CreateJob --> Sys_AssignCO[System Assigns Contractor:<br>Match Skills<br>Check Availability<br>Optimize Route]:::system
        
        %% --- EMERGENCY: CALL CO ---
        Sys_AssignCO --> Dec_EmergencyFlag{Emergency<br>Flag Set?}:::logic
        Dec_EmergencyFlag -- Yes --> Tetra_Call_CO[Tetra Calls CO:<br>Alert Emergency Visit<br>Confirm Availability]:::tetra
        Tetra_Call_CO --> Dec_CO_Avail{CO<br>Available?}:::logic
        Dec_CO_Avail -- Yes --> Sys_NotifyBoth
        Dec_CO_Avail -- No --> Sys_Reassign[Reassign to<br>Available CO]:::system
        Sys_Reassign --> Sys_NotifyBoth
        Dec_EmergencyFlag -- No --> Sys_NotifyBoth
        
        Sys_NotifyBoth[System Sends Confirmations:<br>HO: Email + SMS<br>CO: App Notification]:::system
        Sys_NotifyBoth --> CSR_ConfirmEnd[CSR Confirms Booking<br>with HO and Ends Call]:::tetra
        CSR_ConfirmEnd --> Confirm_Window
    end

    %% =============================================
    %% PHASE 3: CONFIRMATION WINDOW
    %% =============================================
    subgraph Phase3[PHASE 3: CONFIRMATION WINDOW]
        direction TB
        
        Confirm_Window[[Confirmation Window<br>24-48 hrs before visit]]:::subprocess
        Confirm_Window --> Sys_ConfirmRemind[System Sends Reminder:<br>HO: Confirm or Reschedule<br>CO: Job Brief]:::system
        
        Sys_ConfirmRemind --> Dec_Resched{Reschedule<br>Requested?}:::logic
        
        Dec_Resched -- No --> Visit_Confirmed([Visit Confirmed]):::terminator
        
        Dec_Resched -- HO Reschedules --> Tetra_HOResched[Tetra Processes Reschedule:<br>Cancel Current Slot<br>Offer New Times]:::tetra
        Tetra_HOResched --> Dec_HOImmediate{HO Picks<br>New Time?}:::logic
        Dec_HOImmediate -- Yes --> Sys_UpdateJob[System Updates Job:<br>New Time<br>Notify CO]:::system
        Sys_UpdateJob --> Confirm_Window
        Dec_HOImmediate -- No --> Reengage_Loop
        
        Dec_Resched -- CO Reschedules --> Sys_Pull_Avail[Pull CO Availability<br>from ServiceTitan]:::system
        Sys_Pull_Avail --> Tetra_COResched[Tetra Notifies HO:<br>Time Change Options]:::tetra
        Tetra_COResched --> Dec_HOAccept{HO Accepts<br>New Time?}:::logic
        Dec_HOAccept -- Yes --> Sys_UpdateJob
        Dec_HOAccept -- No --> Reengage_Loop
        
        Reengage_Loop[[Re-engagement Loop]]:::subprocess
        Reengage_Loop --> Tetra_Outreach[Tetra Multi-Touch Outreach:<br>Day 1: SMS<br>Day 2: Email<br>Day 3: Call]:::tetra
        Tetra_Outreach --> Dec_Respond{HO<br>Responds?}:::logic
        Dec_Respond -- Yes --> Tetra_HOResched
        Dec_Respond -- No --> Tetra_MarkCold[Mark Lead Cold<br>Remove from CO Calendar<br>Add to Long-Term Nurture]:::tetra
        Tetra_MarkCold --> End_Cancelled([Job Cancelled]):::terminator
    end

    %% =============================================
    %% PHASE 4: VISIT DAY
    %% =============================================
    subgraph Phase4[PHASE 4: VISIT DAY]
        direction TB
        
        Visit_Confirmed --> Tetra_DayOf[Day-Of Monitoring:<br>CO En Route<br>HO Notified]:::monitor
        
        Tetra_DayOf --> CO_Checkin[CO Checks In<br>via App]:::co
        CO_Checkin --> Tetra_Monitor2[Monitor: CO Arrived]:::monitor
        
        Tetra_Monitor2 --> Dec_HOHome{HO<br>Home?}:::logic
        
        Dec_HOHome -- No --> CO_Wait[CO Waits<br>Attempts Contact]:::co
        CO_Wait --> Dec_NoShow{HO<br>Responds?}:::logic
        Dec_NoShow -- Yes --> CO_Diagnose
        Dec_NoShow -- No --> Tetra_LogNoShow[Log No-Show:<br>Record in ServiceTitan<br>Trigger Re-engagement]:::tetra
        Tetra_LogNoShow --> Reengage_Loop
        
        Dec_HOHome -- Yes --> CO_Diagnose[CO Diagnoses Issue]:::co
    end

    %% =============================================
    %% PHASE 5: DIAGNOSIS & SCOPE
    %% =============================================
    subgraph Phase5[PHASE 5: DIAGNOSIS AND SCOPE]
        direction TB
        
        CO_Diagnose --> Sys_GenScope[System Generates Scope:<br>Recommended Fix<br>Line Items<br>Pricing]:::system
        Sys_GenScope --> CO_Review[CO Reviews<br>and Edits Scope]:::co
        
        %% --- PAYMENT FLAG CHECK BEFORE PRESENTING SCOPE ---
        CO_Review --> Dec_Payment_Flag_Scope{Payment<br>Flag Set?}:::logic
        
        Dec_Payment_Flag_Scope -- Fully Covered --> CO_Present_NoPay[CO Presents Scope:<br>No Payment Required]:::co
        Dec_Payment_Flag_Scope -- Labor Warranty --> CO_Present_PartsOnly[CO Presents Scope:<br>Parts Only Pricing]:::co
        Dec_Payment_Flag_Scope -- Parts Warranty --> CO_Present_LaborOnly[CO Presents Scope:<br>Labor Only Pricing]:::co
        Dec_Payment_Flag_Scope -- No Flag --> CO_Present_Full[CO Presents Scope:<br>Full Pricing]:::co
        
        CO_Present_NoPay --> Dec_Repair
        CO_Present_PartsOnly --> Dec_Repair
        CO_Present_LaborOnly --> Dec_Repair
        CO_Present_Full --> Dec_Repair
        
        Dec_Repair{HO<br>Decision?}:::logic
        
        Dec_Repair -- Decline --> Tetra_DiagFee[Process Diagnostic Fee:<br>Check Payment Flags<br>Collect or Waive]:::tetra
        Tetra_DiagFee --> End_DiagOnly([Diagnostic Complete]):::terminator
        
        Dec_Repair -- Replace --> CO_SetLead[CO Sets<br>Replacement Lead]:::co
        CO_SetLead --> Tetra_PayLeadComm[Pay CO<br>Lead Commission]:::tetra
        Tetra_PayLeadComm --> Tetra_Ecom[Handoff to Ecom:<br>Create Replacement Quote<br>Schedule Consultation]:::tetra
        Tetra_Ecom --> End_Ecom([Ecom Handoff]):::terminator
        
        Dec_Repair -- Repair + Replace --> CO_SetLead_Both[CO Sets<br>Replacement Lead]:::co
        CO_SetLead_Both --> Tetra_PayLeadComm_Both[Pay CO<br>Lead Commission]:::tetra
        Tetra_PayLeadComm_Both --> HO_Approve
        
        Dec_Repair -- Approve --> HO_Approve[HO Approves Scope<br>and Payment]:::user
        HO_Approve --> Dec_Parts{Parts<br>Needed?}:::logic
    end

    %% =============================================
    %% PHASE 6: PARTS & SCHEDULING
    %% =============================================
    subgraph Phase6[PHASE 6: PARTS AND FOLLOW-UP]
        direction TB
        
        Dec_Parts -- No --> Dec_SameDay{Same-Day<br>Repair?}:::logic
        
        Dec_Parts -- Yes --> Alert_Tetra[Alert Tetra:<br>Parts Needed]:::tetra
        Alert_Tetra --> Tetra_OrderParts[Tetra Orders Parts:<br>Identify Supplier<br>Place Order<br>Track Shipment]:::tetra
        Tetra_OrderParts --> Sys_PartsETA[System Notifies HO and CO:<br>Parts ETA<br>Expected Delivery Date]:::system
        Sys_PartsETA --> Tetra_TrackParts[Tetra Tracks Parts:<br>Monitor Shipment<br>Alert on Delays]:::monitor
        Tetra_TrackParts --> Parts_Arrive[Parts Arrive<br>at CO Warehouse]:::system
        Parts_Arrive --> Sys_NotifyArrival[System Notifies CO:<br>Parts Ready for Pickup]:::system
        Sys_NotifyArrival --> Sched_Followup
        
        Dec_SameDay -- Yes --> CO_Execute
        Dec_SameDay -- No --> Tetra_ExplainDelay[Tetra Communicates to HO:<br>Why Delay Needed<br>Set Expectations]:::tetra
        Tetra_ExplainDelay --> Sched_Followup
        
        Sched_Followup[[Schedule Follow-Up]]:::subprocess
        Sched_Followup --> HO_FollowSlot[HO Selects<br>Follow-Up Time]:::user
        HO_FollowSlot --> Sys_CreateFollowup[System Creates Follow-Up Job:<br>Link to Original<br>Carry Forward Flags]:::system
        Sys_CreateFollowup --> Confirm_Window
    end

    %% =============================================
    %% PHASE 7: EXECUTION & COMPLETION
    %% =============================================
    subgraph Phase7[PHASE 7: EXECUTION AND COMPLETION]
        direction TB
        
        CO_Execute[CO Performs Repair]:::co
        CO_Execute --> Tetra_Monitor3[Monitor: Job In Progress]:::monitor
        
        Tetra_Monitor3 --> CO_Complete[CO Marks Complete<br>Uploads Photos]:::co
        CO_Complete --> HO_Sign[HO Signs Off]:::user
    end

    %% =============================================
    %% PHASE 8: PAYMENT COLLECTION (After Repair)
    %% =============================================
    subgraph Phase8[PHASE 8: PAYMENT COLLECTION]
        direction TB
        
        HO_Sign --> CO_Collect[CO Collects Payment]:::co
        CO_Collect --> Dec_HO_Pays{HO<br>Pays?}:::logic
        
        %% --- PAYMENT COLLECTED ---
        Dec_HO_Pays -- Yes --> Tetra_Payment[Tetra Processes Payment:<br>Apply Warranty Credits<br>Charge Appropriate Amount]:::tetra
        Tetra_Payment --> Sys_Report[System Generates Reports:<br>HO: Summary + Invoice<br>CO: Summary + Payment]:::system
        Sys_Report --> Tetra_PayCO[Tetra Pays Contractor:<br>Calculate Commission<br>Process Payment]:::tetra
        Tetra_PayCO --> Sys_UpdateRecords[System Updates Records:<br>Digital Twin<br>Service History<br>Warranty Usage]:::system
        Sys_UpdateRecords --> End_Complete([Journey Complete]):::terminator
        
        %% --- PAYMENT NOT COLLECTED ---
        Dec_HO_Pays -- No --> CO_Log_NoPay[CO Logs: Payment<br>Not Collected]:::co
        CO_Log_NoPay --> Tetra_Collection[Tetra Reaches Out<br>for Payment Collection]:::tetra
        Tetra_Collection --> Dec_Collection{Payment<br>Collected?}:::logic
        Dec_Collection -- Yes --> Tetra_Payment_Late[Tetra Processes<br>Late Payment]:::tetra
        Tetra_Payment_Late --> Sys_Report_Late[Send Reports]:::system
        Sys_Report_Late --> Tetra_PayCO_Late[Pay CO<br>After Collection]:::tetra
        Tetra_PayCO_Late --> End_Complete_Late([Journey Complete - Late Pay]):::terminator
        Dec_Collection -- No --> Tetra_Flag_Unpaid[Flag: Unpaid<br>Send to Collections]:::tetra
        Tetra_Flag_Unpaid --> End_Unpaid([Unpaid - Collections]):::terminator
    end

    %% =============================================
    %% ESCALATION PATHS (Cross-Phase)
    %% =============================================
    subgraph Escalations[ESCALATION HANDLING]
        direction TB
        
        Esc_Trigger([Escalation Triggered]):::alert
        Esc_Trigger --> Tetra_Esc_Triage[Tetra Triages Escalation:<br>Identify Issue Type<br>Assess Severity]:::tetra
        
        Tetra_Esc_Triage --> Dec_EscType{Escalation<br>Type?}:::logic
        
        Dec_EscType -- Complaint --> Tetra_Complaint[Handle Complaint:<br>Review Job History<br>Contact HO<br>Resolve or Escalate]:::tetra
        Dec_EscType -- Dispute --> Tetra_Dispute[Handle Dispute:<br>Review Scope and Pricing<br>Mediate Resolution]:::tetra
        Dec_EscType -- CO Issue --> Tetra_COIssue[Handle CO Issue:<br>Reassign if Needed<br>Document Performance]:::tetra
        Dec_EscType -- System Error --> Tetra_SysError[Handle System Error:<br>Manual Override<br>Log for Engineering]:::tetra
        
        Tetra_Complaint --> Esc_Resolved([Escalation Resolved]):::terminator
        Tetra_Dispute --> Esc_Resolved
        Tetra_COIssue --> Esc_Resolved
        Tetra_SysError --> Esc_Resolved
    end