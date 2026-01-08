```mermaid

%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

flowchart TD
    %% --- STYLING ---
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef co fill:#fff8e1,stroke:#ffa000,color:#5d4037,stroke-width:2px
    classDef tetra fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20,stroke-width:2px
    classDef system fill:#f5f5f5,stroke:#333333,color:#333333,stroke-width:1px,stroke-dasharray: 5 5
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef subprocess fill:#f4f4f4,stroke:#333333,stroke-width:2px,color:#000000
    classDef monitor fill:#e3f2fd,stroke:#1565c0,color:#0d47a1,stroke-width:2px

    %% =============================================
    %% PHASE 1: INTAKE & AI DIAGNOSIS
    %% =============================================
    subgraph Phase1[PHASE 1: INTAKE & AI DIAGNOSIS]
        direction TB
        
        %% --- ENTRY POINTS ---
        subgraph Entry["Entry"]
            direction TB
            Start_Web([HO Lands on RepairBot]):::user
            Start_Phone([HO Calls In]):::user
        end

        %% --- WEB PATH ---
        Start_Web -- "New/Install issue" --> User_EntersAddress[Enters Address]:::user
        
        %% --- PHONE PATH ---
        Start_Phone --> Dec_Answer{Call<br>Answered?}:::logic
        Dec_Answer -- Yes --> User_EntersAddress

        Dec_Answer -- No --> Tetra_Callback[[HO sch flow]]:::subprocess
        Tetra_Callback --> User_EntersAddress
        
        %% --- ALREADY SCHEDULED CHECK ---
        User_EntersAddress --> Dec_AlreadyScheduled{Upcoming<br>repair visit?}:::logic
        Dec_AlreadyScheduled -- Yes --> HO_ViewHandoff[View Service Handoff]:::user
        HO_ViewHandoff --> Click_Resch[Click resch button]:::user
        HO_ViewHandoff -- "Ans Qs" --> Exit_Session([Exit session]):::terminator
        HO_ViewHandoff --> Click_Cancel[cancel visit]:::user
        Click_Cancel --> Marketing_Reengage_Cancel([Marketing<br>Re-engagement]):::terminator

        Dec_AlreadyScheduled -- No --> User_DescribesIssue[Describes Issue]:::user

        %% --- DIGITAL TWIN & TRIAGE ---
        User_DescribesIssue --> Dec_Confidence{High Confidence<br>in HVAC?}:::logic
        Dec_Confidence -- No --> HO_ConfirmEquip[HO Confirms Equipment]:::user
        HO_ConfirmEquip --> HO_Triage
        Dec_Confidence -- Yes --> HO_Triage

        HO_Triage[Chat with RepairBot]:::user
        HO_Triage --> HO_ClickSchedule[Click Schedule]:::user
    end

    %% --- CROSS-PHASE CONNECTIONS TO PHASE 2 ---
    HO_ClickSchedule --> Dec_WantsSchedule
    Click_Resch --> Dec_WantsSchedule

    %% =============================================
    %% PHASE 2: SCHEDULING
    %% =============================================
    subgraph Phase2[PHASE 2: SCHEDULING]
        direction TB
        
        Dec_WantsSchedule[View available times]:::user
        Dec_WantsSchedule --> Tetra_InformPremium[Inform of Premium]:::tetra
        Tetra_InformPremium --> Dec_SelectVisit{Select visit?}:::logic

        Dec_SelectVisit -- Yes --> Dec_ContactInfo{Contact info?}:::logic
        Dec_ContactInfo -- Yes --> Sys_CreateJob[Create Job in<br>ServiceTitan]:::system
        Dec_ContactInfo -- No --> Enter_ContactInfo[Enter contact info]:::user
        Enter_ContactInfo --> Sys_CreateJob

        Dec_SelectVisit -- No --> Exit_Session_Sch([Exit session]):::terminator
        
        Sys_CreateJob --> CO_JobAppears[Job Appears on<br>CO Calendar]:::co
        
        %% --- URGENT PATH: CO CONFIRMATION ---
        CO_JobAppears --> Dec_UrgentFlag{Emergency/<br>After-Hours/<br>Within 2 Hrs?}:::logic
        
        Dec_UrgentFlag -- Yes --> Tetra_CallCO[[CO Follow Up:<br>Confirm Availability]]:::subprocess
        Tetra_CallCO --> Dec_COAvailable{CO<br>Available?}:::logic
        Dec_COAvailable -- Yes --> Tetra_ConfirmVisit[Confirm visit]:::tetra
        Tetra_ConfirmVisit --> Sys_SendConfirm
        Dec_COAvailable -- No --> Backref_ServiceHandoff[[Phase 1: Service Handoff]]:::subprocess
        Backref_ServiceHandoff --> Backref_ClickCancel[[Phase 1: Click cancel]]:::subprocess
        Backref_ClickCancel --> HO_Followup_CO[[HO sch flow]]:::subprocess
        
        %% --- STANDARD PATH: AUTO-CONFIRM ---
        Dec_UrgentFlag -- No --> Sys_SendConfirm[Send Confirmation<br>Email/SMS]:::system
    end

    %% --- CROSS-PHASE CONNECTIONS TO PHASE 3 ---
    Sys_SendConfirm --> CO_ReviewHandoff

    %% =============================================
    %% PHASE 3: CONFIRMATION
    %% =============================================
    subgraph Phase3[PHASE 3: CONFIRMATION]
        direction TB
        
        %% --- CO PREPARES ---
        CO_ReviewHandoff[CO Reviews<br>Service Handoff]:::co
        CO_ReviewHandoff --> Monitor_Confirm[[Confirmation]]:::subprocess
        
        %% --- MONITORING WINDOW ---
        Monitor_Confirm --> Monitor_EnRoute[[CO En Route]]:::subprocess
        Monitor_EnRoute --> Dec_ChangeSchTime{Change Schedule<br>Time?}:::logic
        
        %% --- CHANGE SCHEDULE TIME (Dashed) ---
        subgraph ChangeSchTime["Change Sch Time"]
            direction TB
            Actor_ReqDiffTime[HO/CO Requests<br>Diff Time]:::user
            Actor_ReqDiffTime --> Dec_Within2Hrs{Within<br>2 Hrs?}:::logic
            Dec_Within2Hrs -- Yes --> Tetra_CallParty[Call other party]:::tetra
            Tetra_CallParty --> HO_Followup_Change[[HO sch flow]]:::subprocess
            Dec_Within2Hrs -- No --> HO_Followup_Change
        end
        style ChangeSchTime fill:none,stroke:#333333,stroke-dasharray: 5 5
        
        %% --- RUNNING LATE (Dashed) ---
        subgraph RunningLate["Running Late"]
            direction TB
            Actor_Late[HO/CO Running Late]:::co
            Actor_Late --> Tetra_CallOther[Call Other Party]:::tetra
            Tetra_CallOther --> Dec_CancelLate{Cancel<br>Visit?}:::logic
            Dec_CancelLate -- Yes --> Phase2_Return[[HO sch flow]]:::subprocess
        end
        Dec_CancelLate -- No --> Tetra_NotifyETA
        style RunningLate fill:none,stroke:#333333,stroke-dasharray: 5 5

        %% --- DECISION ROUTING ---
        Dec_ChangeSchTime -- Yes --> ChangeSchTime
        Dec_ChangeSchTime -- Delayed --> RunningLate
        Dec_ChangeSchTime -- No --> Tetra_NotifyETA

        Tetra_NotifyETA[Notify with ETA]:::tetra
    end

    %% --- CROSS-PHASE CONNECTION TO PHASE 4 ---
    Tetra_NotifyETA --> CO_Arrive

    %% =============================================
    %% PHASE 4: REPAIR VISIT
    %% =============================================
    subgraph Phase4[PHASE 4: REPAIR VISIT]
        direction TB
        
        %% --- ARRIVAL ---
        CO_Arrive[CO Arrives at Home]:::co
        CO_Arrive --> HO_GreetCO[HO Greets CO]:::user

        %% --- DIAGNOSIS ---
        HO_GreetCO --> CO_Checkin[CO Starts Visit via App]:::co
        CO_Checkin --> CO_ReviewIssue[CO Reviews Issue<br>with HO]:::co
        CO_ReviewIssue --> CO_Diagnose[CO + AI diagnose issue]:::co
        CO_Diagnose --> Sys_GenerateScope[System Generates<br>Pricing/Line Items]:::system
        Sys_GenerateScope --> CO_ReviewWithHO[CO reviews diagnosis<br>with HO]:::co
        
        %% --- HO DECISION ---
        CO_ReviewWithHO -- "Interest in replacing" --> Click_SendToSales[Click send to sales]:::user
        Click_SendToSales --> Sales_FollowUp[[Sales follow up]]:::subprocess
        CO_ReviewWithHO --> Dec_RepairNeeded{Repair<br>needed?}:::logic
        Dec_RepairNeeded -- Yes --> Dec_HODecision{HO wants<br>repair?}:::logic
        Dec_RepairNeeded -- No --> CompleteVisit[Complete Visit]:::co

        %% --- DECLINE PATH ---
        Dec_HODecision -- No --> CompleteVisit
        
        %% --- APPROVE PATH ---
        Dec_HODecision -- Yes --> Dec_SameDay{Same-Day<br>Repair?}:::logic
        
        %% --- SAME DAY REPAIR ---
        Dec_SameDay -- Yes --> CO_Execute[CO Performs Repair]:::co
        CO_Execute --> CO_Photo[CO Takes Verification<br>Photos]:::co
        CO_Photo --> CO_MarkComplete[CO Marks Complete]:::co
        CO_MarkComplete --> HO_SignOff[HO Signs Off on Work]:::user
        HO_SignOff --> CompleteVisit
        
        %% --- FOLLOW-UP NEEDED ---
        Dec_SameDay -- No --> Dec_ReturnVisit
        
        Phase4_ScheduleSub[[HO sch flow]]:::subprocess

        CompleteVisit --> HO_FinalReport[HO Receives<br>Final Report via Email/SMS]:::user
        HO_FinalReport --> Dec_CollectPayment{Collect payment<br>in-house?}:::logic
    end

    %% --- CROSS-PHASE CONNECTIONS TO PHASE 5 ---
    Dec_CollectPayment -- Yes --> Tetra_ProcessPayment
    Dec_CollectPayment -- No --> Tetra_NotifyOps

    %% =============================================
    %% PHASE 5: FOLLOW UP
    %% =============================================
    subgraph Phase5[PHASE 5: FOLLOW UP]
        direction TB

        %% --- REMIND CO (Dashed) ---
        subgraph RemindCO["Remind CO"]
            direction TB
            FourHrsCheck[">4 hrs since<br>visit start"]:::monitor
            FourHrsCheck --> Tetra_CheckIn[[CO Follow Up:<br>Check-in]]:::subprocess
            Tetra_CheckIn --> Dec_COForgot{CO forgot to<br>close visit?}:::logic
            Dec_COForgot -- No --> Tetra_CloseVisit24
            Dec_COForgot -- Yes --> Tetra_RemindCO[Remind CO]:::tetra
            Tetra_RemindCO --> Tetra_CloseVisit24[Close visit<br>after 24 hours]:::tetra
        end
        style RemindCO fill:none,stroke:#333333,stroke-dasharray: 5 5

        %% --- PAYMENT (Dashed) ---
        subgraph Payment["Payment"]
            direction TB
            Tetra_ProcessPayment[Process Payment]:::tetra
            Tetra_ProcessPayment --> PayCO[[Pay CO]]:::subprocess

            Tetra_NotifyOps[Notify Ops]:::tetra
            Tetra_NotifyOps --> Collections[[Collections]]:::subprocess
        end
        style Payment fill:none,stroke:#333333,stroke-dasharray: 5 5
        
        %% --- RETURN VISIT (Dashed) ---
        subgraph ReturnVisit["Parts Ordering"]
            direction TB
            Dec_ReturnVisit{Order parts?}:::logic
            Dec_ReturnVisit -- Yes --> Tetra_PartsAlert[Parts Alert Received]:::tetra
            Dec_ReturnVisit -- No --> Phase4_ScheduleSub
            Tetra_PartsAlert --> Tetra_OrderParts[Order Parts]:::tetra
            Tetra_OrderParts --> Tetra_MonitorShipment[Monitor Shipment]:::tetra
            Tetra_MonitorShipment --> Tetra_ConfirmReceived[Confirm CO/HO<br>Received Parts]:::tetra
            Tetra_ConfirmReceived --> Scheduling_Sub[[HO sch flow]]:::subprocess
        end
        style ReturnVisit fill:none,stroke:#333333,stroke-dasharray: 5 5
        
    end