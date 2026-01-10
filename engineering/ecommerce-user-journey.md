```mermaid

%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%

flowchart TD
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef subprocess fill:#f4f4f4,stroke:#333333,stroke-width:2px

    %% =============================================
    %% PHASE 1: ENTRY
    %% =============================================
    subgraph Phase1_Entry[PHASE 1: ENTRY]
        direction TB
        
        %% --- ENTRY POINT ---
        Start([HO Lands on Onboarding Page]) --> Dec_ContactComplete{Contact Info Complete?}:::logic
        
        Dec_ContactComplete -- Missing Contact Info --> HO_EnterContact[Enter Contact Information]:::user
        HO_EnterContact --> Dec_AddressServiceable{Address Serviceable?}:::logic
        
        Dec_ContactComplete -- Complete --> Dec_AddressServiceable2{Address Serviceable?}:::logic
        
        Dec_AddressServiceable -- Not Serviceable --> OutOfTerritory([Out of Territory - Dead End]):::terminator
        Dec_AddressServiceable2 -- Not Serviceable --> OutOfTerritory
        
        Dec_AddressServiceable -- NY Address without Household Income --> HO_EnterContact
    end

    Dec_AddressServiceable -- Serviceable --> HO_Welcome
    Dec_AddressServiceable2 -- Serviceable --> HO_Welcome

    %% =============================================
    %% PHASE 2: HOME ASSESSMENT
    %% =============================================
    subgraph Phase2_HomeAssessment[PHASE 2: HOME ASSESSMENT]
        direction TB
        
        HO_Welcome[Welcome]:::user
        HO_Welcome --> Dec_QuestionsForm{useQuestionsForm Feature Flag?}:::logic
        
        %% --- DEFAULT PATH (useQuestionsForm = false) ---
        Dec_QuestionsForm -- "false (default)" --> HO_ConfirmHomeDetails[Confirm Home Details]:::user
        HO_ConfirmHomeDetails --> HO_Compatibility[System Compatibility]:::user
        HO_Compatibility --> HO_Sizing[Home Sizing]:::user
        HO_Sizing --> HO_RebatePrograms[Rebate Programs]:::user
        HO_RebatePrograms --> HO_RebateResults[Rebate Results]:::user
        HO_RebateResults --> HO_SavingsOverview[Savings Overview]:::user
        
        %% --- ALTERNATIVE PATH (useQuestionsForm = true) ---
        Dec_QuestionsForm -- "true (query param)" --> HO_QuestionsForm[Consolidated Questions Form]:::user
        
        %% --- CHAT WITH EXPERT ACCESS ---
        HO_ConfirmHomeDetails -- Chat with Expert --> ChatWithExpert[[Chat with Expert]]:::subprocess
    end

    HO_SavingsOverview --> HO_BrowseSystems
    HO_QuestionsForm --> HO_BrowseSystems

    %% =============================================
    %% PHASE 3: PRODUCT SELECTION
    %% =============================================
    subgraph Phase3_ProductSelection[PHASE 3: PRODUCT SELECTION]
        direction TB
        
        HO_BrowseSystems[Browse Systems]:::user
        HO_BrowseSystems --> HO_SystemDetails[System Details]:::user
        
        HO_SystemDetails --> Dec_HasAddOns{System Has Add-ons?}:::logic
        
        Dec_HasAddOns -- Yes --> HO_SelectAddOns[Select Add-ons]:::user
        Dec_HasAddOns -- No --> HO_ReviewOrder
        
        HO_SystemDetails -- Error State --> ChatWithExpert2[[Chat with Expert]]:::subprocess
        ChatWithExpert2 --> HO_ReviewOrder
    end

    HO_SelectAddOns --> HO_ReviewOrder

    %% =============================================
    %% PHASE 4: CHECKOUT
    %% =============================================
    subgraph Phase4_Checkout[PHASE 4: CHECKOUT]
        direction TB
        
        HO_ReviewOrder[Review Order]:::user
        HO_ReviewOrder --> HO_Payment[Payment]:::user
        HO_Payment --> Success([Order Complete]):::terminator
    end