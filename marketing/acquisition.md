```mermaid
flowchart TB
    %% Styles
    classDef marketing fill:#e1f5fe,stroke:#01579b,stroke-width:2px,color:#000
    classDef sales fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:#000
    classDef decision fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000
    classDef exit fill:#ffebee,stroke:#c62828,stroke-width:2px,color:#000
    classDef success fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px,color:#000
    classDef subprocess fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px,color:#000

    subgraph Impression
        ad[Advertisement]:::marketing
        imp[Impression]:::marketing
        web[Website]:::marketing
        form{Form?}:::decision
        ad --> imp
        imp --> web
        web --> form
        form -->|No| ad
    end

    subgraph Lead
        answered{Answered?}:::decision
        ecom[Ecommerce]:::sales
        sdr[SDR Call]:::sales
        plp{PLP?}:::decision
        outreach_lead[[Outreach]]:::subprocess
        
        answered -->|No| outreach_lead
        answered -->|Yes| sdr
        ecom --> plp
        plp -->|No| outreach_lead
        outreach_lead --> sdr
        outreach_lead --> ecom
    end

    subgraph MQL
        outreach_mql[[Outreach]]:::subprocess
        sch[Scheduled]:::sales
        attend{Attend?}:::decision
        ae[AE Call]:::sales
        qual[Qualified]:::success
        unq[Unqualified]:::exit
        
        outreach_mql --> ae
        sch --> attend
        attend -->|No| outreach_mql
        attend -->|Yes| ae
        ae --> qual
        ae --> unq
    end

    %% Cross-subgraph connections
    imp -->|HO calls| answered
    form -->|Yes| ecom
    plp -->|Yes| outreach_mql
    plp -->|Yes-Sch| sch
    sdr --> sch
    sdr --> unq

    %% White subgraph backgrounds
    style Impression fill:#fff,stroke:#333
    style Lead fill:#fff,stroke:#333
    style MQL fill:#fff,stroke:#333