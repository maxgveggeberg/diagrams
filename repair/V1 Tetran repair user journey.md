```mermaid
%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

flowchart TD
    %% --- STYLING ---
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef co fill:#fff8e1,stroke:#ffa000,color:#5d4037,stroke-width:2px
    classDef tetra fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20,stroke-width:2px
    classDef system fill:#f5f5f5,stroke:#333333,color:#333333,stroke-width:1px,stroke-dasharray: 5 5
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef alert fill:#fff0e6,stroke:#d9534f,color:#d9534f,stroke-width:2px
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef subprocess fill:#f4f4f4,stroke:#333333,stroke-width:2px,color:#000000
    classDef monitor fill:#e3f2fd,stroke:#1565c0,color:#0d47a1,stroke-width:2px

    %% =============================================
    %% PHASE 1: AI DIAGNOSIS
    %% =============================================
    subgraph Phase1[PHASE 1: AI DIAGNOSIS]
        direction TB
        
        Start([HO Calls In]) --> Dec_Answer{Call<br>Answered?}:::logic
        
        %% =============================================
        %% CALL ANSWERED PATH
        %% =============================================
        Dec_Answer -- Yes --> Dec_Existing{Existing<br>Customer?}:::logic
        
        %% --- NEW CUSTOMER PATH ---
        Dec_Existing -- No --> CSR_CreateNew[Create New Customer<br>in Admin]:::tetra
        CSR_CreateNew --> CSR_OpenRepairBot[Opens RepairBot]:::tetra
        CSR_OpenRepairBot --> CSR_Entry[CSR Enters Address<br>and Issue Description]:::tetra
        CSR_Entry --> CSR_Triage[CSR Guides Triage<br>via RepairBot]:::tetra
        
        %% --- EXISTING CUSTOMER PATH ---
        Dec_Existing -- Yes --> CSR_LookupAdmin[Look up in Admin]:::tetra
        CSR_LookupAdmin --> Dec_RequestType{Request<br>Type?}:::logic
        
        %% --- INSTALL ISSUE ---
        Dec_RequestType -- Install Issue --> CSR_CreateCase[Create Case]:::tetra
        CSR_CreateCase --> CSR_Triage
        
        %% --- QUESTION OR ISSUE ---
        Dec_RequestType -- Question or Issue --> CSR_AttemptHandle[CSR Attempts<br>to Handle]:::tetra
        CSR_AttemptHandle -- Resolved --> CSR_UpdateCase([Update Admin]):::terminator
        CSR_AttemptHandle -- Not Resolved --> Escalation_Sub[[Escalation]]:::subprocess
        
        %% --- CHANGE SCHEDULE TIME ---
        Dec_RequestType -- Change Sch Time --> CSR_CancelOriginal[Cancel Original Visit]:::tetra
        
        %% =============================================
        %% CALL NOT ANSWERED PATH
        %% =============================================
        Dec_Answer -- No --> HO_Followup[[HO Follow Up]]:::subprocess
    end

    %% =============================================
    %% PHASE 2: SCHEDULING
    %% =============================================
    subgraph Phase2[PHASE 2: SCHEDULING]
        direction TB
        
        %% --- CHANGE SCHEDULE TIME (from existing customer) ---
        CSR_CancelOriginal --> Dec_Schedule
        
        %% --- NEW BOOKING PATH ---
        CSR_Triage --> Dec_Schedule{HO Wants to<br>Schedule?}:::logic
        
        %% --- NO - RESCHEDULE OR CANCEL PATH ---
        Dec_Schedule -- No --> Dec_ReschedOrCancel{Reschedule/Cancel/<br>Not Interested?}:::logic
        Dec_ReschedOrCancel --> CSR_UpdateAdmin[Update Admin]:::tetra
        CSR_UpdateAdmin -- Reschedule --> HO_Followup2[[HO Follow Up]]:::subprocess
        CSR_UpdateAdmin -- Cancel/Not Interested --> Marketing_Reengage([Marketing<br>Re-engagement]):::terminator
        
        %% --- YES - VISIT TYPE PATH ---
        Dec_Schedule -- Yes --> Dec_VisitType{Visit<br>Type?}:::logic
        
        %% --- AFTER HOURS / EMERGENCY ---
        Dec_VisitType -- After Hours/Emergency --> CSR_InformPremium[Inform of Premium]:::tetra
        CSR_InformPremium --> CSR_SchVisit[Sch Visit &<br>Collect Contact Info]:::tetra

        %% --- WITHIN 2 HRS ---
        Dec_VisitType -- Within 2 Hrs --> CSR_SchVisit
        
        %% --- STANDARD ---
        Dec_VisitType -- Standard --> CSR_SchVisitStd[Sch Visit &<br>Collect Contact Info]:::tetra
    end

    %% --- CONNECTIONS TO PHASE 3 ---
    CSR_SchVisit --> CSR_CallCO
    CSR_SchVisitStd --> Monitor_Confirm

    %% =============================================
    %% PHASE 3: CONFIRMATION
    %% =============================================
    subgraph Phase3[PHASE 3: CONFIRMATION]
        direction TB

        %% --- CALL CO TO CONFIRM ---
        CSR_CallCO[Call CO to Confirm]:::tetra
        CSR_CallCO --> Dec_Confirmed{Confirmed?}:::logic
        Dec_Confirmed -- Yes --> CSR_UpdateAdmin3[Update Admin]:::tetra
        CSR_UpdateAdmin3 --> Monitor_Confirm[[Confirmation]]:::subprocess

        %% --- NOT AVAILABLE PATH ---
        Dec_Confirmed -- Not Available --> CSR_CancelVisit[Cancel Visit]:::tetra
        CSR_CancelVisit --> HO_Followup_Phase3[[HO Follow Up]]:::subprocess

        %% --- UNRESPONSIVE PATH ---
        Dec_Confirmed -- Unresponsive --> CO_Followup[[CO Follow Up]]:::subprocess
        CO_Followup --> Dec_Confirmed

        %% --- MONITORING ---
        Monitor_Confirm --> Monitor_EnRoute[[CO En Route]]:::subprocess

        %% --- CHANGE SCHEDULE TIME DECISION ---
        Monitor_EnRoute --> Dec_ChangeSchTime{Change Schedule<br>Time?}:::logic

        %% --- CHANGE SCH TIME (dotted subgraph) ---
        subgraph ChangeSchTime["Change Sch Time"]
            direction TB
            HO_CO_ReqDiffTime[HO/CO Requests<br>Diff Time]:::tetra
            HO_CO_ReqDiffTime --> CancelVisit_Sch[Cancel Visit]:::tetra
            CancelVisit_Sch --> Dec_Within2Hrs{Within<br>2 Hrs?}:::logic
            Dec_Within2Hrs -- Yes --> CallHOCO[Call HO/CO]:::tetra
            CallHOCO --> HO_Followup3[[HO Follow Up]]:::subprocess
            Dec_Within2Hrs -- No --> HO_Followup3
        end
        style ChangeSchTime fill:none,stroke:#333333,stroke-dasharray: 5 5

        %% --- RUNNING LATE (dotted subgraph) ---
        subgraph RunningLate["Running Late"]
            direction TB
            HO_CO_Late[HO/CO Running Late]:::tetra
            HO_CO_Late --> CallOtherParty["Call Other Party<br>(HO/CO)"]:::tetra
            CallOtherParty --> Dec_CancelVisitLate{Cancel<br>Visit?}:::logic
            Dec_CancelVisitLate -- Yes --> Phase2Scheduling_RL[[Phase 2: Scheduling]]:::subprocess
            Dec_CancelVisitLate -- No --> NotifyWithETA[Notify HO/CO<br>with ETA]:::tetra
        end
        style RunningLate fill:none,stroke:#333333,stroke-dasharray: 5 5

        %% --- CONNECTIONS FROM DECISION NODE ---
        Dec_ChangeSchTime -- Yes --> ChangeSchTime
        Dec_ChangeSchTime -- Delayed --> RunningLate
        Dec_ChangeSchTime -- No --> Monitor_VisitStarts
    end

    %% --- NOTIFY ETA TO VISIT ---
    NotifyWithETA --> Monitor_VisitStarts

    %% =============================================
    %% PHASE 4: REPAIR VISIT
    %% =============================================
    subgraph Phase4[PHASE 4: REPAIR VISIT]
        direction TB

        Monitor_VisitStarts[Visit Starts]:::monitor
        Monitor_VisitStarts --> Dec_HOInterested{HO Interested in<br>Replacement?}:::logic
        Dec_HOInterested -- Yes --> SalesCheckIn
        Dec_HOInterested -- Yes or No --> FourHrsCheck[">4 hrs since<br>visit start"]:::monitor
        FourHrsCheck --> CallCOCheckIn[[Call CO to check-in]]:::subprocess
        CallCOCheckIn --> Dec_COForgot{CO forgot to<br>close visit?}:::logic
        Dec_COForgot -- No --> VisitComplete[Visit Complete]:::monitor
        Dec_COForgot -- Yes --> RemindCO[Remind CO]:::tetra
        RemindCO --> CloseVisit24[Close visit<br>after 24 hours]:::tetra
        CloseVisit24 --> VisitComplete
    end

    %% --- CONNECTION FROM VISIT COMPLETE TO PHASE 5 ---
    VisitComplete --> SalesCheckIn
    VisitComplete --> Dec_PaymentCollected

    %% =============================================
    %% PHASE 5: FOLLOW UP
    %% =============================================
    subgraph Phase5[PHASE 5: FOLLOW UP]
        direction TB
        
        %% --- SALES (dotted subgraph) ---
        subgraph Sales["Sales"]
            direction TB
            
            %% --- SALES CHECK-IN FLOW ---
            SalesCheckIn[[Sales Check-in]]:::subprocess
            SalesCheckIn --> Dec_SpeakWithHO{Speak<br>with HO?}:::logic
            Dec_SpeakWithHO -- No --> ContinueJourney([Continue Journey]):::terminator
            Dec_SpeakWithHO -- Yes --> AskAboutExperience[Ask About Experience]:::tetra
            AskAboutExperience --> DocumentIssues[Document Issues]:::tetra
            DocumentIssues --> OpsReview[[Ops Review]]:::subprocess
            AskAboutExperience --> Dec_InterestOtherServices{Interest in<br>Other Services?}:::logic
            Dec_InterestOtherServices -- Yes --> Install_Sub([Install]):::terminator
            Dec_InterestOtherServices -- No --> Marketing_Reengage_Sales([Marketing<br>Re-engagement]):::terminator
        end
        style Sales fill:none,stroke:#333333,stroke-dasharray: 5 5
        
        %% --- PAYMENT (dotted subgraph) ---
        subgraph Payment["Payment"]
            direction TB
            Dec_PaymentCollected{Payment<br>Collected?}:::logic
            Dec_PaymentCollected -- Yes --> ProcessPayment[Process Payment]:::tetra
            ProcessPayment --> PayCO[[Pay CO]]:::subprocess
            Dec_PaymentCollected -- No --> InternalCollections[[Internal Collections]]:::subprocess
            InternalCollections --> Dec_PaymentCollected2{Payment<br>Collected?}:::logic
            Dec_PaymentCollected2 -- Yes --> ProcessPayment
            Dec_PaymentCollected2 -- No --> SendToCollections[[Send to Collections]]:::subprocess
        end
        style Payment fill:none,stroke:#333333,stroke-dasharray: 5 5
        
        %% --- RETURN VISIT (dotted subgraph) ---
        subgraph ReturnVisit["Return Visit"]
            direction TB
            Dec_ReturnVisit{Return Visit<br>Needed?}:::logic
            Dec_ReturnVisit -- No --> Marketing_Reengage3([Marketing<br>Re-engagement]):::terminator
            Dec_ReturnVisit -- Yes --> Dec_PartsNeeded{Parts<br>Needed?}:::logic
            Dec_PartsNeeded -- No --> Dec_Scheduled{Scheduled?}:::logic
            Dec_PartsNeeded -- Yes --> PartsAlert[Parts Alert Received]:::tetra
            PartsAlert --> OrderParts[Order Parts]:::tetra
            OrderParts --> MonitorShipment[Monitor Shipment]:::tetra
            MonitorShipment --> ConfirmReceived[Confirm CO/HO<br>Received Parts]:::tetra
            ConfirmReceived --> Dec_Scheduled
            Dec_Scheduled -- No --> Scheduling_Sub[[Phase 2: Scheduling]]:::subprocess
            Dec_Scheduled -- Yes --> Monitoring_Sub[[Phase 4: Repair Visit]]:::subprocess
        end
        style ReturnVisit fill:none,stroke:#333333,stroke-dasharray: 5 5
        
        %% --- CONNECTION FROM PAYMENT TO RETURN VISIT ---
        ProcessPayment --> Dec_ReturnVisit
        
    end