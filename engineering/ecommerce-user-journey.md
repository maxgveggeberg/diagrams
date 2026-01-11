```mermaid

%%{init: {"flowchart": {"defaultRenderer": "elk"}} }%%
flowchart TD
    classDef user fill:#e6f3ff,stroke:#08427b,color:#08427b,stroke-width:2px
    classDef logic fill:#ffffff,stroke:#08427b,stroke-width:2px,color:#08427b
    classDef terminator fill:#333333,stroke:#333333,color:#ffffff
    classDef offramp fill:#ffebee,stroke:#c62828,color:#b71c1c,stroke-width:2px
    classDef questionnaire fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20,stroke-width:2px
    classDef checkout fill:#fff8e1,stroke:#ffa000,color:#5d4037,stroke-width:2px

    subgraph Phase1_Entry["Phase 1: Landing Page"]
        Start(["User Arrives"]):::terminator
        Landing["/onboarding - Landing Page"]:::user
        Dec_Serviceable{{"Address Serviceable?"}}:::logic
        ContactInfo["/contact-info - Collect Name, Email, Phone, Address"]:::user
        Dec_HasContactInfo{{"Has Contact Info?"}}:::logic
        Dec_NYState{{"NY State & Missing Household Info?"}}:::logic
        EnhancedRebates["/enhanced-rebates - Collect Household Income"]:::user
    end

    subgraph Phase1_OffRamps["OFF-RAMPS: DISQUALIFICATION"]
        direction TB
        OutOfTerritory["/out-of-territory - Not Serviceable"]:::offramp
        NotEligibleHomeType["/not-eligible-home-type"]:::offramp
        NotEligibleIncentives["/not-eligible-for-incentives"]:::offramp
        NotRightProviderHomeOwnership["/not-the-right-provider-home-ownership"]:::offramp
        NotRightProviderFuelAssistance["/not-the-right-provider-fuel-assistance"]:::offramp
        NotReady["/not-ready"]:::offramp
        OutOfTerritory ~~~ NotEligibleHomeType ~~~ NotEligibleIncentives ~~~ NotRightProviderHomeOwnership ~~~ NotRightProviderFuelAssistance ~~~ NotReady
    end

    subgraph Phase2_Welcome["PHASE 2: WELCOME & HOME CONFIRMATION"]
        Welcome["/welcome - Welcome Animation"]:::user
        HomeLayoutGeneration["Home Layout Generation Animation"]:::user
        ConfirmHomeInfo["/confirm-home-info - Review Prefilled Home Data"]:::user
    end

    subgraph Phase3_Compatibility["Phase 3: Compatibility"]
        HousingType["/housing-type"]:::questionnaire
        HomeOwnership["/home-ownership"]:::questionnaire
        HeatingSystem["/heating-system"]:::questionnaire
        HeatingFuel["/heating-fuel"]:::questionnaire
        SystemAge["/system-age"]:::questionnaire
        AC["/ac"]:::questionnaire
    end

    subgraph Phase4_Sizing["Phase 4: Sizing"]
        SqFt["/sqft"]:::questionnaire
        NumberOfFloors["/number-of-floors"]:::questionnaire
        RoomsInHome["/rooms-in-home"]:::questionnaire
        BedroomsInHome["/bedrooms-in-home"]:::questionnaire
        Bathrooms["/bathrooms"]:::questionnaire
        BasementsInHome["/basements-in-home"]:::questionnaire
        SystemLocation["/system-location"]:::questionnaire
        InstallLocationHeight["/install-location-height"]:::questionnaire
        CondenserLocation["/condenser-location"]:::questionnaire
        Thermostats["/thermostats"]:::questionnaire
    end

    subgraph Phase5_Savings["Phase 5: Savings"]
        UtilityProvider["/utility-provider"]:::questionnaire
        Dec_NeedHEA{{"Needs HEA Assessment?"}}:::logic
        HEAConfirmation["/hea-confirmation"]:::questionnaire
        HEAInfo["/hea-info"]:::questionnaire
        Insulation["/insulation"]:::questionnaire
        InsulationRequirement["/insulation-requirement"]:::questionnaire
        RebatePrograms["/rebate-programs"]:::questionnaire
        RebateResults["/rebate-results"]:::questionnaire
        Results["/results"]:::questionnaire
    end

    subgraph Phase6_ProductSelection["Phase 6: Product Selection"]
        ProductListing["/product-listing - Compare Systems"]:::checkout
        ProductDetails["/product-details - System Details"]:::checkout
        AddOns["/add-ons - Select Add-ons"]:::checkout
    end

    subgraph Phase7_Checkout["Phase 7: Checkout & Completion"]
        Review["/review - Review Selection"]:::checkout
        Deposit["/deposit - $100 Refundable Deposit"]:::checkout
        Success["/success - Booking Confirmed"]:::checkout
        ChatWithExpert["/chat-with-an-expert - Schedule Call"]:::checkout
    end

    %% Phase 1 Connections
    Start --> Landing
    Landing --> Dec_Serviceable
    Dec_Serviceable -- "No" --> OutOfTerritory
    Dec_Serviceable -- "Yes" --> Dec_HasContactInfo
    Dec_HasContactInfo -- "No" --> ContactInfo
    ContactInfo --> Dec_Serviceable
    Dec_HasContactInfo -- "Yes" --> Dec_NYState
    Dec_NYState -- "Yes" --> EnhancedRebates
    EnhancedRebates --> Welcome
    Dec_NYState -- "No" --> Welcome

    %% Phase 2 Connections
    Welcome --> HomeLayoutGeneration
    HomeLayoutGeneration --> ConfirmHomeInfo
    ConfirmHomeInfo --> HousingType

    %% Phase 3 Category Flow
    HousingType --> HomeOwnership
    HomeOwnership --> HeatingSystem
    HeatingSystem --> HeatingFuel
    HeatingFuel --> SystemAge
    SystemAge --> AC
    AC --> SqFt

    %% Sizing Category
    SqFt --> NumberOfFloors
    NumberOfFloors --> RoomsInHome
    RoomsInHome --> BedroomsInHome
    BedroomsInHome --> Bathrooms
    Bathrooms --> BasementsInHome
    BasementsInHome --> SystemLocation
    SystemLocation --> InstallLocationHeight
    InstallLocationHeight --> CondenserLocation
    CondenserLocation --> Thermostats

    %% Savings Category
    Thermostats --> UtilityProvider
    UtilityProvider --> Dec_NeedHEA
    Dec_NeedHEA -- "Yes (MA)" --> HEAConfirmation
    HEAConfirmation --> HEAInfo
    HEAInfo --> Insulation
    Insulation --> InsulationRequirement
    InsulationRequirement --> RebatePrograms
    RebatePrograms --> RebateResults
    Dec_NeedHEA -- "No" --> RebateResults

    %% Compare Category
    RebateResults --> Results
    Results --> ProductListing

    %% Phase 6 Connections
    ProductListing --> ProductDetails
    ProductDetails --> AddOns
    AddOns --> Review

    %% Phase 5 Connections
    Review --> Deposit
    Deposit --> Success
    
    %% Error/Expert Path
    ProductDetails -. "Error State" .-> ChatWithExpert
    Success --> ChatWithExpert