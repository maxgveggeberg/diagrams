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
        
        Start([HO Calls In]) --> CSR_Answer[CSR Answers Call\nOpens CSR RepairBot]:::tetra
        CSR_Answer --> CSR_Entry[CSR Enters Address\nand Issue Description\nin RepairBot]:::tetra
        
        CSR_Entry --> Dec_Existing{Existing\nCustomer?}:::logic
        
        Dec_Existing -- Yes --> Sys_PullFlags[System Pulls Customer Flags:\nPlan Status\nWarranty Status\nService History]:::system
        Sys_PullFlags --> Dec_FullyCovered{Fully\nCovered?}:::logic
        
        Dec_FullyCovered -- Yes --> Flag_NoPay[Set Flag:\nNo Payment Required]:::system
        Dec_FullyCovered -- No --> Dec_Warranty{Labor\nWarranty?}:::logic
        Dec_Warranty -- Yes --> Flag_Labor[Set Flag:\nLabor Covered]:::system
        Dec_Warranty -- No --> Dec_PartsWarranty{Parts\nWarranty?}:::logic
        Dec_PartsWarranty -- Yes --> Flag_Parts[Set Flag:\nParts Covered]:::system
        Dec_PartsWarranty -- No --> Sys_StandardFlags[Set Flag:\nStandard Billing]:::system
        
        Flag_NoPay --> Dec_Confidence
        Flag_Labor --> Dec_Confidence
        Flag_Parts --> Dec_Confidence
        Sys_StandardFlags --> Dec_Confidence
        
        Dec_Existing -- No --> Flag_NewCust[Flag: New Customer\nDigital Twin Required]:::system
        Flag_NewCust --> Dec_Confidence
        
        %% --- HOME DETAILS CONFIDENCE CHECK ---
        Dec_Confidence{High Confidence\nin HVAC Details?}:::logic
        
        Dec_Confidence -- Yes --> Dec_Emergency
        
        Dec_Confidence -- No --> CSR_AskEquip[CSR Asks HO:\nWhat heating system\ndo you have?]:::tetra
        CSR_AskEquip --> Dec_HOKnows{HO Knows\nEquipment?}:::logic
        
        Dec_HOKnows -- Yes --> CSR_RecordEquip[CSR Records:\nHeating Type\nFuel Type\nAC Status]:::tetra
        Dec_HOKnows -- No --> CSR_Guide[CSR Guides HO:\nCheck unit label\nDescribe location\nDescribe appearance]:::tetra
        CSR_Guide --> CSR_RecordEquip
        
        CSR_RecordEquip --> Sys_UpdateTwin[System Updates\nDigital Twin]:::system
        Sys_UpdateTwin --> Dec_Emergency
        
        %% --- EMERGENCY CHECK ---
        Dec_Emergency{Emergency\nVisit?}:::logic
        Dec_Emergency -- Yes --> CSR_ValidateEmerg[CSR Validates Emergency:\nReview Issue Description\nConfirm Urgency with HO]:::tetra
        CSR_ValidateEmerg --> Dec_RealEmerg{Genuine\nEmergency?}:::logic
        Dec_RealEmerg -- Yes --> Flag_Emergency[Set Flag:\nEmergency + Premium Rate]:::alert
        Dec_RealEmerg -- No --> CSR_Downgrade[CSR Informs HO:\nStandard Pricing Applies]:::tetra
        CSR_Downgrade --> Bot_Triage
        Flag_Emergency --> Bot_Triage
        
        Dec_Emergency -- No --> Bot_Triage[RepairBot: Triage Symptoms\nand Safety Check]:::system
    end

    %% =============================================
    %% PHASE 2: SCHEDULING & ASSIGNMENT
    %% =============================================
    subgraph Phase2[PHASE 2: SCHEDULING AND ASSIGNMENT]
        direction TB
        
        Bot_Triage --> Dec_Schedule{HO Wants to\nSchedule?}:::logic
        
        Dec_Schedule -- No --> CSR_OfferReport[CSR Offers to Send\nChat Report via Email]:::tetra
        CSR_OfferReport --> Dec_WantReport{HO Wants\nReport?}:::logic
        Dec_WantReport -- Yes --> CSR_CollectContact[CSR Collects\nHO Contact Info]:::tetra
        CSR_CollectContact --> Sys_SendReport[System Sends\nChat Report Email]:::system
        Sys_SendReport --> End_NoSchedule([Call End:\nLead Captured]):::terminator
        Dec_WantReport -- No --> End_Exit([Call End:\nNo Capture]):::terminator
        
        Dec_Schedule -- Yes --> CSR_ShowCal[CSR Reviews Available\nTime Slots with HO]:::tetra
        CSR_ShowCal --> HO_SelectSlot[HO Selects Time Slot]:::user
        HO_SelectSlot --> CSR_ConfirmContact[CSR Confirms Contact Info:\nName, Phone, Email]:::tetra
        CSR_ConfirmContact --> Sys_CreateJob[System Creates Job\nin ServiceTitan:\nAttach All Flags\nSet Priority Level]:::system
        
        Sys_CreateJob --> Sys_AssignCO[System Assigns Contractor:\nMatch Skills\nCheck Availability\nOptimize Route]:::system
        
        Sys_AssignCO --> Sys_NotifyBoth[System Sends Confirmations:\nHO: Email + SMS\nCO: App Notification]:::system
        Sys_NotifyBoth --> CSR_ConfirmEnd[CSR Confirms Booking\nwith HO and Ends Call]:::tetra
        CSR_ConfirmEnd --> Confirm_Window
    end

    %% =============================================
    %% PHASE 3: CONFIRMATION WINDOW
    %% =============================================
    subgraph Phase3[PHASE 3: CONFIRMATION WINDOW]
        direction TB
        
        Confirm_Window[[Confirmation Window\n24-48 hrs before visit]]:::subprocess
        Confirm_Window --> Sys_ConfirmRemind[System Sends Reminder:\nHO: Confirm or Reschedule\nCO: Job Brief]:::system
        
        Sys_ConfirmRemind --> Dec_Resched{Reschedule\nRequested?}:::logic
        
        Dec_Resched -- No --> Visit_Confirmed([Visit Confirmed]):::terminator
        
        Dec_Resched -- HO Reschedules --> Tetra_HOResched[Tetra Processes Reschedule:\nCancel Current Slot\nOffer New Times]:::tetra
        Tetra_HOResched --> Dec_HOImmediate{HO Picks\nNew Time?}:::logic
        Dec_HOImmediate -- Yes --> Sys_UpdateJob[System Updates Job:\nNew Time\nNotify CO]:::system
        Sys_UpdateJob --> Confirm_Window
        Dec_HOImmediate -- No --> Reengage_Loop
        
        Dec_Resched -- CO Reschedules --> Tetra_COResched[Tetra Processes Reschedule:\nGet New Time from CO\nNotify HO with Options]:::tetra
        Tetra_COResched --> Dec_HOAccept{HO Accepts\nNew Time?}:::logic
        Dec_HOAccept -- Yes --> Sys_UpdateJob
        Dec_HOAccept -- No --> Reengage_Loop
        
        Reengage_Loop[[Re-engagement Loop]]:::subprocess
        Reengage_Loop --> Tetra_Outreach[Tetra Multi-Touch Outreach:\nDay 1: SMS\nDay 2: Email\nDay 3: Call]:::tetra
        Tetra_Outreach --> Dec_Respond{HO\nResponds?}:::logic
        Dec_Respond -- Yes --> Tetra_HOResched
        Dec_Respond -- No --> Tetra_MarkCold[Mark Lead Cold\nRemove from CO Calendar\nAdd to Long-Term Nurture]:::tetra
        Tetra_MarkCold --> End_Cancelled([Job Cancelled]):::terminator
    end

    %% =============================================
    %% PHASE 4: VISIT DAY
    %% =============================================
    subgraph Phase4[PHASE 4: VISIT DAY]
        direction TB
        
        Visit_Confirmed --> Tetra_DayOf[Day-Of Monitoring:\nCO En Route\nHO Notified]:::monitor
        
        Tetra_DayOf --> CO_Checkin[CO Checks In\nvia App]:::co
        CO_Checkin --> Tetra_Monitor2[Monitor: CO Arrived]:::monitor
        
        Tetra_Monitor2 --> Dec_HOHome{HO\nHome?}:::logic
        
        Dec_HOHome -- No --> CO_Wait[CO Waits\nAttempts Contact]:::co
        CO_Wait --> Dec_NoShow{HO\nResponds?}:::logic
        Dec_NoShow -- Yes --> CO_Diagnose
        Dec_NoShow -- No --> Tetra_LogNoShow[Log No-Show:\nRecord in ServiceTitan\nTrigger Re-engagement]:::tetra
        Tetra_LogNoShow --> Reengage_Loop
        
        Dec_HOHome -- Yes --> CO_Diagnose[CO Diagnoses Issue]:::co
    end

    %% =============================================
    %% PHASE 5: DIAGNOSIS & DECISION
    %% =============================================
    subgraph Phase5[PHASE 5: DIAGNOSIS AND DECISION]
        direction TB
        
        CO_Diagnose --> Sys_GenScope[System Generates Scope:\nRecommended Fix\nLine Items\nPricing]:::system
        Sys_GenScope --> CO_Review[CO Reviews\nand Edits Scope]:::co
        CO_Review --> CO_Present[CO Presents\nScope to HO]:::co
        
        CO_Present --> Dec_Repair{HO\nDecision?}:::logic
        
        Dec_Repair -- Decline --> Tetra_DiagFee[Process Diagnostic Fee:\nCheck Payment Flags\nCollect or Waive]:::tetra
        Tetra_DiagFee --> End_DiagOnly([Diagnostic Complete]):::terminator
        
        Dec_Repair -- Replace --> Tetra_Ecom[Handoff to Ecom:\nCreate Replacement Quote\nSchedule Consultation]:::tetra
        Tetra_Ecom --> End_Ecom([Ecom Handoff]):::terminator
        
        Dec_Repair -- Approve --> Dec_Parts{Parts\nNeeded?}:::logic
    end

    %% =============================================
    %% PHASE 6: PARTS & SCHEDULING
    %% =============================================
    subgraph Phase6[PHASE 6: PARTS AND FOLLOW-UP]
        direction TB
        
        Dec_Parts -- No --> Dec_SameDay{Same-Day\nRepair?}:::logic
        
        Dec_Parts -- Yes --> Tetra_OrderParts[Tetra Orders Parts:\nIdentify Supplier\nPlace Order\nTrack Shipment]:::tetra
        Tetra_OrderParts --> Sys_PartsETA[System Notifies HO and CO:\nParts ETA\nExpected Delivery Date]:::system
        Sys_PartsETA --> Tetra_TrackParts[Tetra Tracks Parts:\nMonitor Shipment\nAlert on Delays]:::monitor
        Tetra_TrackParts --> Parts_Arrive[Parts Arrive\nat CO Warehouse]:::system
        Parts_Arrive --> Sys_NotifyArrival[System Notifies CO:\nParts Ready for Pickup]:::system
        Sys_NotifyArrival --> Sched_Followup
        
        Dec_SameDay -- Yes --> CO_Execute
        Dec_SameDay -- No --> Tetra_ExplainDelay[Tetra Communicates to HO:\nWhy Delay Needed\nSet Expectations]:::tetra
        Tetra_ExplainDelay --> Sched_Followup
        
        Sched_Followup[[Schedule Follow-Up]]:::subprocess
        Sched_Followup --> Sys_CreateFollowup[System Creates Follow-Up Job:\nLink to Original\nCarry Forward Flags]:::system
        Sys_CreateFollowup --> HO_FollowSlot[HO Selects\nFollow-Up Time]:::user
        HO_FollowSlot --> Sys_ConfirmFollowup[System Confirms Follow-Up:\nNotify HO and CO\nUpdate Calendar]:::system
        Sys_ConfirmFollowup --> CO_Execute
    end

    %% =============================================
    %% PHASE 7: EXECUTION & COMPLETION
    %% =============================================
    subgraph Phase7[PHASE 7: EXECUTION AND COMPLETION]
        direction TB
        
        CO_Execute[CO Performs Repair]:::co
        CO_Execute --> Tetra_Monitor3[Monitor: Job In Progress]:::monitor
        
        Tetra_Monitor3 --> CO_Complete[CO Marks Complete\nUploads Photos]:::co
        CO_Complete --> HO_Sign[HO Signs Off]:::user
        
        HO_Sign --> Tetra_Payment[Tetra Processes Payment:\nCheck Flags\nApply Warranty Credits\nCharge Appropriate Amount]:::tetra
        
        Tetra_Payment --> Dec_PayFlag{Payment\nFlag?}:::logic
        Dec_PayFlag -- Fully Covered --> Tetra_NoPay[Waive Payment\nLog Plan Usage]:::tetra
        Dec_PayFlag -- Labor Warranty --> Tetra_PartsOnly[Charge Parts Only\nLog Warranty Usage]:::tetra
        Dec_PayFlag -- Parts Warranty --> Tetra_LaborOnly[Charge Labor Only\nLog Warranty Usage]:::tetra
        Dec_PayFlag -- Standard --> Tetra_FullPay[Process Full Payment]:::tetra
        
        Tetra_NoPay --> Sys_Report
        Tetra_PartsOnly --> Sys_Report
        Tetra_LaborOnly --> Sys_Report
        Tetra_FullPay --> Sys_Report
        
        Sys_Report[System Generates and Sends Report:\nHO: Service Summary + Invoice\nCO: Job Summary + Payment\nInternal: Analytics Update]:::system
        
        Sys_Report --> Tetra_PayCO[Tetra Pays Contractor:\nCalculate Commission\nProcess Payment]:::tetra
        Tetra_PayCO --> Sys_UpdateRecords[System Updates Records:\nDigital Twin\nService History\nWarranty Usage]:::system
        Sys_UpdateRecords --> End_Complete([Journey Complete]):::terminator
    end

    %% =============================================
    %% ESCALATION PATHS (Cross-Phase)
    %% =============================================
    subgraph Escalations[ESCALATION HANDLING]
        direction TB
        
        Esc_Trigger([Escalation Triggered]):::alert
        Esc_Trigger --> Tetra_Esc_Triage[Tetra Triages Escalation:\nIdentify Issue Type\nAssess Severity]:::tetra
        
        Tetra_Esc_Triage --> Dec_EscType{Escalation\nType?}:::logic
        
        Dec_EscType -- Complaint --> Tetra_Complaint[Handle Complaint:\nReview Job History\nContact HO\nResolve or Escalate]:::tetra
        Dec_EscType -- Dispute --> Tetra_Dispute[Handle Dispute:\nReview Scope and Pricing\nMediate Resolution]:::tetra
        Dec_EscType -- CO Issue --> Tetra_COIssue[Handle CO Issue:\nReassign if Needed\nDocument Performance]:::tetra
        Dec_EscType -- System Error --> Tetra_SysError[Handle System Error:\nManual Override\nLog for Engineering]:::tetra
        
        Tetra_Complaint --> Esc_Resolved([Escalation Resolved]):::terminator
        Tetra_Dispute --> Esc_Resolved
        Tetra_COIssue --> Esc_Resolved
        Tetra_SysError --> Esc_Resolved
    end