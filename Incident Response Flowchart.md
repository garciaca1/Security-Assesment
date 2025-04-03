flowchart TD
    A[Incident Detection] --> B{Threat Severity Assessment}
    
    B -->|Low Risk| C[Log and Monitor]
    B -->|Medium Risk| D[Detailed Investigation]
    B -->|High Risk| E[Immediate Containment]
    
    subgraph "Low-Risk Path"
        C --> C1[Update Threat Database]
        C1 --> C2[Generate Observation Report]
    end
    
    subgraph "Medium-Risk Investigation"
        D --> D1[Gather Forensic Evidence]
        D1 --> D2[Analyze Potential Impact]
        D2 --> D3{Confirm Threat?}
        D3 -->|Yes| E
        D3 -->|No| C
    end
    
    subgraph "High-Risk Mitigation"
        E --> E1[Isolate Affected Systems]
        E1 --> E2[Block Malicious Sources]
        E2 --> E3[Prevent Service Disruption]
        E3 --> F[Comprehensive Incident Report]
    end
    
    F --> G{Require Player Notification?}
    G -->|Yes| H[Communicate Security Event]
    G -->|No| I[Internal Review]
    
    H --> J[Implement Additional Safeguards]
    I --> J
    
    J --> K[Update Security Protocols]
    K --> L[Continuous Monitoring Resumed]
    
    classDef detection fill:#f96,stroke:#333,stroke-width:2px;
    classDef investigation fill:#9f6,stroke:#333,stroke-width:2px;
    classDef mitigation fill:#69f,stroke:#333,stroke-width:2px;
    
    class A,B detection;
    class D,D1,D2 investigation;
    class E,E1,E2,F mitigation;
