# Configuration Management System Project Prompt

## Project Overview
Building a unified configuration management system inspired by UCI (Unified Configuration Interface) with enhanced features including memory management, caching, and distributed storage capabilities.

## System Architecture
```
[Application/Service Layer]
           ↓
[Configuration Management Interface]
           ↓
[Data Structure Manager] ← → [Memory Manager]
           ↓                      ↓
[Cache Manager (L1/L2/L3)]
           ↓
[Storage Abstraction Layer]
           ↓
[Storage Backends]
  ├─ uthash Memory-Mapped Storage (Fast Access)
  ├─ File Storage (/etc/config/)
  └─ PostgreSQL (Dynamic Data/Tables)
```

## Core Features
- Centralized configuration interface
- Multiple access methods (CLI, C API, Python, DBus)
- Distributed storage with central management
- Thread-safe operations
- Module-specific configurations
- Efficient caching system
- Memory-optimized storage
- History tracking
- Access control

## Configuration File Structure
```
package 'example'

config 'module' 'auth'
    option 'db_path' '/var/db/auth.db'
    option 'sha_key' 'abc123'
    option 'cache_policy' 'LRU'
    option 'cache_size' '100M'
    list 'allowed_clients' '192.168.1.100'
    list 'allowed_clients' '192.168.1.101'

config 'field' 'username'
    option 'type' 'string'
    option 'size' '32'
    option 'access' 'read-write'
    option 'cache' 'true'
    option 'validation' 'required|alpha_num'
```

## Tasks Breakdown

### 1. Project Setup
- [ ] Initialize project structure
- [ ] Set up build system (CMake)
- [ ] Create config directory structure
- [ ] Set up version control
- [ ] Configure CI/CD pipeline
- [ ] Create development guidelines

### 2. Core Components
- [ ] Implement configuration parser
- [ ] Create command-line interface
- [ ] Build configuration validator
- [ ] Implement access control system
- [ ] Create configuration storage manager
- [ ] Build module registration system
- [ ] Implement event notification system

### 3. Storage Layer
- [ ] Create file storage backend
- [ ] Implement PostgreSQL backend
- [ ] Add memory-mapped storage
- [ ] Create storage abstraction interface
- [ ] Implement backup functionality
- [ ] Add versioning support
- [ ] Create migration tools

### 4. Cache System
- [ ] Implement multi-level cache
- [ ] Add cache policies (LRU, MRU, FIFO)
- [ ] Create cache invalidation system
- [ ] Implement cache statistics
- [ ] Add cache compression
- [ ] Create cache persistence
- [ ] Implement cache synchronization

### 5. Memory Management
- [ ] Create memory pool system
- [ ] Implement memory allocation strategies
- [ ] Add memory defragmentation
- [ ] Create memory usage tracking
- [ ] Implement memory limits
- [ ] Add memory optimization
- [ ] Create memory diagnostics

### 6. Security Features
- [ ] Implement authentication system
- [ ] Add access control lists
- [ ] Create audit logging
- [ ] Implement encryption
- [ ] Add SHA key management
- [ ] Create security policies
- [ ] Implement rate limiting

### 7. API Layer
- [ ] Create C library API
- [ ] Implement Python bindings
- [ ] Add DBus interface
- [ ] Create REST API
- [ ] Implement WebSocket support
- [ ] Add API versioning
- [ ] Create API documentation

### 8. Command Line Interface
- [ ] Implement basic commands (get, set, list)
- [ ] Add batch operations
- [ ] Create search functionality
- [ ] Implement export/import
- [ ] Add formatting options
- [ ] Create interactive mode
- [ ] Implement completion

### 9. Testing System
- [ ] Create unit tests
- [ ] Implement integration tests
- [ ] Add performance benchmarks
- [ ] Create stress tests
- [ ] Implement security tests
- [ ] Add validation tests
- [ ] Create regression tests

### 10. uthash Integration
- [ ] Set up uthash headers
- [ ] Implement memory-mapped storage using uthash
- [ ] Create hash table management functions
- [ ] Add array support using utarray
- [ ] Implement table structures
- [ ] Add dynamic data type support
- [ ] Create index management
- [ ] Implement fast lookup operations
- [ ] Add bulk operation support
- [ ] Create data structure templates
- [ ] Implement atomic operations
- [ ] Add migration utilities
- [ ] Create backup/restore for hash tables

### 11. Transaction Management
```c
struct Transaction {
    uint64_t id;                // Transaction ID
    time_t start_time;          // Transaction start time
    enum TxState {
        TX_ACTIVE,
        TX_COMMITTED,
        TX_ROLLED_BACK
    } state;
    struct {
        void* old_value;       // Previous value
        void* new_value;       // New value
        char* key;            // Affected key
    }* changes;
    size_t change_count;       // Number of changes
    UT_hash_handle hh;         // uthash handle
};

struct TransactionManager {
    struct Transaction* active_tx;   // Active transactions
    pthread_mutex_t tx_lock;         // Transaction lock
    uint64_t last_tx_id;            // Last transaction ID
    bool auto_commit;               // Auto-commit flag
};
```
### 12. Event System
```c
typedef void (*EventCallback)(void* data, void* context);

struct EventSubscriber {
    char* subscriber_id;         // Subscriber identifier
    EventCallback callback;      // Callback function
    void* context;              // Callback context
    UT_hash_handle hh;          // uthash handle
};

struct EventTopic {
    char* topic_name;           // Topic name
    struct EventSubscriber* subscribers;  // Hash table of subscribers
    pthread_mutex_t lock;       // Topic lock
    UT_hash_handle hh;          // uthash handle
};

struct EventManager {
    struct EventTopic* topics;  // Hash table of topics
    pthread_mutex_t lock;       // Manager lock
    size_t max_subscribers;     // Maximum subscribers per topic
};
```
### 13. Schema Management
```c
struct SchemaVersion {
    uint32_t major;              // Major version
    uint32_t minor;              // Minor version
    uint32_t patch;              // Patch version
    char* description;           // Version description
    time_t created_at;           // Creation timestamp
    struct {
        char* sql;              // SQL migration script
        char* rollback;         // Rollback script
    } migration;
    UT_hash_handle hh;          // uthash handle
};

// Schema registry
struct SchemaRegistry {
    struct SchemaVersion* versions;  // Hash table of versions
    uint32_t current_major;         // Current major version
    uint32_t current_minor;         // Current minor version
    uint32_t current_patch;         // Current patch version
    char* module_name;              // Associated module
    pthread_mutex_t lock;           // Schema lock
};
```
### 14. Monitoring and Metrics
- [ ] Add performance metrics
- [ ] Create health checks
- [ ] Implement alerting
- [ ] Add resource monitoring
- [ ] Create usage statistics
- [ ] Implement trend analysis
- [ ] Add monitoring dashboard

## Data Structures

### Configuration Entry
```c
struct ConfigEntry {
    char* key;
    char* value;
    enum ValueType type;
    size_t size;
    uint32_t access_flags;
    struct CachePolicy cache_policy;
    struct ValidationRules validation;
    time_t last_modified;
    struct ConfigEntry* next;
};
```

### Module Configuration
```c
struct ModuleConfig {
    char* module_name;
    char* sha_key;
    char* db_path;
    struct CacheConfig cache_config;
    struct StorageConfig storage_config;
    struct AccessControl access_control;
    struct ConfigEntry* entries;
    pthread_rwlock_t lock;
};
```

### Data Structures with uthash

### Hash Table Entry
```c
#include "uthash.h"

// Basic key-value entry with hash table support
struct ConfigHashEntry {
    char* key;                     // key string
    union {
        char* str_value;          // string value
        int64_t int_value;        // integer value
        double float_value;       // float value
        void* ptr_value;          // pointer to complex data
    } value;
    enum ValueType value_type;     // type of the value
    uint32_t flags;                // metadata flags
    UT_hash_handle hh;            // uthash handle
};
```

```c
// Array/List support using utarray
#include "utarray.h"

struct ConfigArray {
    char* key;                     // array name
    UT_array* array;              // dynamic array
    enum ValueType element_type;   // type of elements
    size_t element_size;           // size of each element
    UT_hash_handle hh;            // for adding to hash tables
};
```

```c
// Table structure using uthash
struct ConfigTable {
    char* name;                    // table name
    struct {
        char* name;               // column name
        enum ValueType type;      // column type
        size_t size;             // column size
    }* columns;
    size_t num_columns;           // number of columns
    struct ConfigHashEntry* rows; // hash table of rows
    UT_hash_handle hh;           // for table registry
};
```

```c
// Memory-mapped configuration store
struct ConfigStore {
    char* segment_name;           // name of memory segment
    size_t segment_size;         // size of memory segment
    void* base_address;          // base address of mapped memory
    struct ConfigHashEntry* entries;  // hash table of entries
    struct ConfigArray* arrays;      // hash table of arrays
    struct ConfigTable* tables;      // hash table of tables
    pthread_rwlock_t lock;          // read-write lock
};
```

### Configuration Templates
```c
// Define configuration templates using X-Macros
#define CONFIG_TYPES                  \
    X(STRING,  char*,        str)    \
    X(INT,     int64_t,     int)    \
    X(FLOAT,   double,      float)   \
    X(BOOL,    bool,        int)     \
    X(ARRAY,   UT_array*,   ptr)     \
    X(TABLE,   void*,       ptr)

// Generate enum for types
enum ValueType {
#define X(name, type, field) TYPE_##name,
    CONFIG_TYPES
#undef X
};

// Template for configuration fields
#define DEFINE_CONFIG_FIELD(key, type, validator) \
    { .key = key,                                \
      .type = TYPE_##type,                       \
      .validator = validator,                     \
      .flags = 0 }
```

## Command Line Interface
```bash
# Basic operations
cms get <module>.<section>.<option>
cms set <module>.<section>.<option>=<value>
cms add <module> <section-type>
cms delete <module>.<section>[.<option>]
cms list <module>[.<section>]
cms commit [<module>]

# Advanced operations
cms export <module> [--format=json]
cms import <module> [--merge]
cms search <pattern>
cms history <module>.<section>
cms validate <module>
```

## Usage Examples

### Basic Configuration Operations
```c
// Initialize configuration store
struct ConfigStore* store = config_store_init("app_config", 1024*1024);

// Set values
config_set_string(store, "server.host", "localhost");
config_set_int(store, "server.port", 8080);
config_set_float(store, "server.timeout", 30.5);

// Get values
char* host = config_get_string(store, "server.host", NULL);
int port = config_get_int(store, "server.port", 8080);
```

### Array Operations
```c
// Create an array of integers
struct ConfigArray* arr;
config_array_create(store, "allowed_ports", TYPE_INT, sizeof(int));

// Add elements
int ports[] = {80, 443, 8080};
config_array_push(store, "allowed_ports", ports, 3);

// Iterate array
int* p = NULL;
while ((p = config_array_next(store, "allowed_ports", p))) {
    printf("Port: %d\n", *p);
}
```

### Table Operations
```c
// Define table structure
struct ConfigTable* users_table = config_table_create(store, "users", 
    (Column[]){
        {"id", TYPE_INT, sizeof(int64_t)},
        {"name", TYPE_STRING, 64},
        {"active", TYPE_BOOL, sizeof(bool)}
    }, 3);

// Insert row
config_table_insert(store, "users", 
    (KeyValue[]){
        {"id", {.int_value = 1}},
        {"name", {.str_value = "admin"}},
        {"active", {.int_value = true}}
    }, 3);

// Query with index
struct ConfigHashEntry* user = config_table_find(store, "users", "id", 1);
```

## Implementation Guidelines
1. Use C for core components
2. Support modular architecture
3. Implement thread-safe operations
4. Use efficient data structures
5. Follow security best practices
6. Maintain backwards compatibility
7. Provide detailed logging
8. Support internationalization

## Performance Requirements
1. Configuration lookup < 50ns (cached)
2. Configuration commit < 100ms
3. Cache hit ratio > 90%
4. Support 10000+ concurrent connections
5. Memory overhead < 5%
6. Thread scaling to 32 cores
7. Configuration file size up to 1GB
8. Support for 1M+ configuration entries
9. Transaction commit time < 1ms
10. Event propagation delay < 100μs
11. Schema migration time < 1s per 1000 entries
12. Bulk operation throughput > 100K ops/sec
13. Maximum event subscribers per topic: 1000
14. Event queue capacity: 1M events
15. Maximum concurrent transactions: 1000
16. Schema version storage overhead < 1MB
17. Event persistence write latency < 500μs
18. Monitoring overhead < 1% CPU


## Reliability Requirements
1. Automatic recovery from crashes
2. Transaction durability guarantee
3. Event delivery guarantee
4. Schema version consistency
5. Backup/restore time < 5 minutes
6. Zero data loss during upgrades
7. Automatic failover < 1 second
8. Maximum recovery time < 30 seconds

## Security Requirements
1. Secure authentication
2. Access control enforcement
3. Audit logging
4. Data encryption
5. Input validation
6. Rate limiting
7. Secure defaults
8. Security policy enforcement
