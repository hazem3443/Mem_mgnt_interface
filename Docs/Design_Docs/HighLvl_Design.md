# High Level Design - Configuration Management System

## System Architecture

```mermaid
flowchart TB
    subgraph Client_Applications
        direction BT
        CLI["Command Line Interface"]
        CAPI["client C APIs"]
        PyAPI["Python sending commands to sdbus or command line execution"]
    end 
    subgraph DBus_Interface_Layer
        direction LR
        CP["Connection Pool"] -->
        AQ["Async Queue"] -->
        MH["Message Handler"] <-->
        ER["Error Recovery"]
    end
    Client_Applications <==> DBus_Interface_Layer
    MH <--> VAL
    

    subgraph Unified_Configuration_Manager
        subgraph Checks
            direction RL
            VAL["Command Validator"] -->
            PERM["Permission Check"] -->
            ROUTE["Command Router"] -->
            TRANS["Transaction Wrapper"] -->
            LOG["Command Logger"]
        end
        VAL --> LOG
        PERM --> LOG
        ROUTE --> LOG

        TRANS <==> DSM
        TRANS <==> CFGM

        subgraph Memory_Manager
            direction TB
            DSM["Status Manager"]
            CFGM["Configuration Manager"]

            subgraph Core["Core Services"]
                direction TB
                TX["Transaction Manager"]
                EV["Event System"]
                SM["Schema Manager"]
                MT["Metrics & Monitoring"]
            end
            Core -->DSM
            Core --> CFGM
            subgraph Cache["Cache System"]
                direction TB
                L1["L1 Cache (Memory)"]
                L2["L2 Cache (Mapped Memory)"]
                L3["L3 Cache (Persistent)"]
            end
            Cache <===> DSM
            Cache ==/COMMIT/==> CFGM

            subgraph Storage["Storage Layer"]
                SAL["Storage Abstraction Layer"]
                MMS["Memory-Mapped Storage (uthash)"]
                FS["File Storage"]
                PG["PostgreSQL"]
            end 
            Storage <===> CFGM
        end
    end
```

## Component Flow Diagrams

### Data Access Flow

```mermaid
sequenceDiagram
    participant C as Client
    participant I as Interface
    participant Ca as Cache
    participant S as Storage
    
    C->>I: Request Data
    I->>Ca: Check Cache
    alt Cache Hit
        Ca-->>I: Return Cached Data
        I-->>C: Return Data
    else Cache Miss
        Ca->>S: Fetch Data
        S-->>Ca: Return Data
        Ca-->>I: Return Data
        I-->>C: Return Data
    end
```

### Transaction Flow

```mermaid
sequenceDiagram
    participant C as Client
    participant T as Transaction Manager
    participant D as Data Manager
    participant S as Storage

    C->>T: Begin Transaction
    T->>D: Lock Resources
    C->>D: Modify Data
    D->>S: Prepare Changes
    alt Success
        C->>T: Commit
        T->>S: Apply Changes
        S-->>T: Success
        T-->>C: Committed
    else Failure
        C->>T: Rollback
        T->>S: Revert Changes
        S-->>T: Reverted
        T-->>C: Rolled Back
    end
```

### Event System Flow

```mermaid
sequenceDiagram
    participant P as Publisher
    participant E as Event Manager
    participant S1 as Subscriber 1
    participant S2 as Subscriber 2
    participant Ca as Cache
    
    S1->>E: Subscribe(topic)
    S2->>E: Subscribe(topic)
    P->>E: Publish Event
    par Parallel Notification
        E->>S1: Notify
        E->>S2: Notify
    end
    E->>Ca: Update Cache
```

## Memory Management Architecture

```mermaid
flowchart TB
    subgraph MM["Memory Manager"]
        MP["Memory Pools"]
        MA["Memory Allocator"]
        MT["Memory Tracker"]
    end
    
    subgraph Cache["Cache Layers"]
        L1["L1: Hot Data"]
        L2["L2: Warm Data"]
        L3["L3: Cold Data"]
    end
    
    subgraph Storage["Storage Systems"]
        MMap["Memory Mapped (uthash)"]
        File["File System"]
        DB["PostgreSQL"]
    end
    
    MM --> Cache
    Cache --> Storage
```

## Data Structure Organization

```mermaid
classDiagram
    class ConfigStore {
        +entries: ConfigHashEntry*
        +arrays: ConfigArray*
        +tables: ConfigTable*
        +init()
        +cleanup()
    }
    
    class ConfigHashEntry {
        +key: string
        +value: union
        +type: ValueType
        +flags: uint32_t
    }
    
    class ConfigArray {
        +key: string
        +array: UT_array*
        +elementType: ValueType
        +elementSize: size_t
    }
    
    class ConfigTable {
        +name: string
        +columns: Column[]
        +rows: ConfigHashEntry*
    }
    
    ConfigStore --> ConfigHashEntry
    ConfigStore --> ConfigArray
    ConfigStore --> ConfigTable
    ConfigTable --> ConfigHashEntry
```

## Security Architecture

```mermaid
flowchart TB
    subgraph Security["Security Layer"]
        Auth["Authentication"]
        ACL["Access Control"]
        Audit["Audit Log"]
        Crypto["Encryption"]
    end
    
    subgraph Access["Access Points"]
        CLI["Command Line"]
        API["API Calls"]
        IPC["IPC/DBus"]
    end
    
    Access --> Security
    Security --> CMS["Config Management"]
```
