```mermaid
flowchart TB
    %% Styles
    classDef marketing fill:#e1f5fe,stroke:#01579b,stroke-width:2px,color:#000
    classDef sales fill:#fff9c4,stroke:#fbc02d,stroke-width:2px,color:#000
    classDef decision fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px,color:#000

    subgraph Advertisement
        direction TB
        
        subgraph Thumbtack
            tt_search[Search]:::marketing
            tt_select[Select]:::marketing
            tt_accept[Accept]:::marketing
            tt_message[Message]:::marketing
            
            tt_search --> tt_select
            tt_select --> tt_accept
            tt_accept --> tt_message
        end
        
        subgraph GoogleAds[Google Ads]
            ga_campaign[Campaign]:::marketing
            ga_search[Search]:::marketing
            ga_impression[Impression]:::marketing
            ga_click[Click]:::marketing
            
            ga_campaign --> ga_search
            ga_search --> ga_impression
            ga_impression --> ga_click
        end
        
        subgraph DirectMail[Direct Mail]
            dm_homes[Homes]:::marketing
            dm_sent[DM Sent]:::marketing
            
            dm_homes --> dm_sent
        end
        
        subgraph Canvassing
            cv_homes[Homes]:::marketing
            cv_knocked[Knocked]:::marketing
            cv_answered[Answered]:::marketing
            
            cv_homes --> cv_knocked
            cv_knocked --> cv_answered
        end
    end

    subgraph Retargeting
        direction LR
        rt_ad[Ad Served]:::marketing
        rt_click[Click]:::marketing
        
        rt_ad --> rt_click
    end

    subgraph Website
        landing[Landing Page]:::marketing
        form{Form?}:::decision
        
        landing --> form
    end

    CreateLead[Create Lead]:::sales

    %% Cross-subgraph connections
    ga_click --> landing
    dm_sent --> landing
    dm_sent -->|call| CreateLead
    tt_accept --> CreateLead
    tt_message --> landing
    rt_click --> landing
    cv_answered --> CreateLead
    form -->|Yes| CreateLead
    form -->|No| rt_ad

    %% White subgraph backgrounds
    style Advertisement fill:#fff,stroke:#333
    style GoogleAds fill:#fff,stroke:#333
    style DirectMail fill:#fff,stroke:#333
    style Thumbtack fill:#fff,stroke:#333
    style Canvassing fill:#fff,stroke:#333
    style Retargeting fill:#fff,stroke:#333
    style Website fill:#fff,stroke:#333