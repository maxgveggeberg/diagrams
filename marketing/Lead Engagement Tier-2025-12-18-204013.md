
```mermaid
flowchart TB
 subgraph ENTRY["📥 NEW SUBSCRIBER ENTRY"]
        A["Lead Signs Up"]
  end
 subgraph WELCOME["📬 WELCOME SERIES (Days 0-10)"]
        B["Day 0: Email + SMS Welcome"]
        C["Day 3: Educational Content"]
        D["Day 7: Case Study"]
        E["Day 10: Soft CTA"]
  end
 subgraph TIERS["📊 ENGAGEMENT TIERS"]
        F["🔥 HOT: 0-30 Days Active 2x/week email, 1x/week SMS"]
        G["🌡️ WARM: 31-60 Days 1x/week email, 2x/month SMS"]
        H["❄️ COOLING: 61-90 Days 2x/month email, 1x/month SMS"]
        I["🧊 COLD: 90+ Days Inactive Re-engagement flow only"]
  end
 subgraph REENGAGEMENT["🔄 RE-ENGAGEMENT FLOW (4 Weeks)"]
        J["Week 1: Soft Touch SMS intro + 2 value emails"]
        K["Week 2: Incentive Layer SMS offer + 2 discount emails"]
        L["Week 3: Urgency Push SMS last chance + final offer"]
        M["Week 4: Sunset Preference update + goodbye"]
  end
 subgraph OUTCOMES["✅ OUTCOMES"]
        N["Converted to Customer"]
        O["Re-Engaged: Back to HOT"]
        P["Suppressed: Remove from List"]
  end
 subgraph METRICS["📈 YOUR KEY FIXES"]
        Q["Switch Thursday → Wednesday for better CTR"]
        R["Use Re: prefix strategically for click lift"]
        S["Stop Pricing Guide emails, 0.35% CTR failing"]
        T["Split MA Lead segments"]
  end
    A --> B
    B --> C
    C --> D
    D --> E
    E --> F
    F -- No activity 30d --> G
    G -- No activity 30d --> H
    H -- No activity 30d --> I
    F -- Converts --> N
    G -- Converts --> N
    I L_I_J_0@-- <br> --> J
    J --> K
    K --> L
    L --> M
    M -- Clicked any message --> O
    M -- No response --> P
    O --> F
    METRICS -. Apply to all stages .-> TIERS
    I -- Click --> F
    H -- Converts --> N


    L_I_J_0@{ curve: natural }