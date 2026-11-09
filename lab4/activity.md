```mermaid
flowchart TD
    subgraph Client
        A((Start)) --> B[Select problem]
        B --> C[Select language]
        C --> D[Write code]
        D --> E[Submit]
    end
    subgraph System
        E --> F[[Create submission with status Pending]]
        F --> G0[[Send code to Judge]]
        S1[[Save final status Accepted]]
        S2[[Save runtime_ms and memory_kb]]
        S3[[Save final status Wrong Answer (SC-08)]]
        S4[[Save final status Time Limit Exceeded (SC-09)]]
        S5[[Save final status Runtime Error or Compilation Error]]
        S6[[Show final status and failure details]]
        D1[[Show Accepted with runtime and memory]]
    end
    subgraph Judge
        G0 --> G1[[Receive code]]
        G1 --> G2[[Send code to CodeRunner]]
        G2 --> G4[[Receive test results from CodeRunner]]
        G4 --> G{Did code pass all test cases?}
        G -->|yes| S1
        S1 --> S2
        S2 --> D1
        G -->|wrong output (SC-08)| S3
        G -->|time limit exceeded (SC-09)| S4
        G -->|runtime or compilation error| S5
        S3 --> S6
        S4 --> S6
        S5 --> S6
    end
```
