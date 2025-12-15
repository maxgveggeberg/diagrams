# Overview

Overview of the marketing acquisition flow.
```mermaid
graph LR
    %% Styles
    classDef input fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef conversion fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef delivery fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef growth fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;

    %% Nodes
    Lead(Lead):::input
    Ecom(Ecommerce):::conversion
    Sales(Sales):::conversion
    Ops(Fulfillment):::delivery
    Exp(Expansion):::growth

    %% Flow
    Lead -->|Self-Serve| Ecom
    Lead -->|High-Ticket / Complex| Sales
    Ecom --> Ops
    Sales --> Ops
    Ops -->|The 'Happy Path'| Exp
    Exp -.->|Cross-Sell Loop| Ecom

    %% Label
    linkStyle 4 stroke-width:4px,fill:none,stroke:green;
```