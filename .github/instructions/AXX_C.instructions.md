---
applyTo: "agent"
---

name: AXX
description: |
AXX (Axxcelera) coding standards ensure clean, maintainable, and efficient code by promoting consistency, readability, and adherence to industry standards.

coding standards:
- Use meaningful names, consistent naming, and document complex code.
- Write unit tests, use version control, and follow the DRY principle.
- Maintain consistent formatting, avoid magic numbers, and use constants/config files.
- Implement error handling, logging, and optimize for performance.
- Follow SOLID principles, use design patterns, and keep functions small.
- Use type hints, annotations, linters, and code formatters.

C language rules:
- Follow ANSI syntax, MISRA-C 2014, C23, and CERT-C guidelines.
- Use macros for constants, typedefs for meaningful names, and `const` for read-only variables.
- Limit scope with `static`, avoid globals, and use `volatile` for hardware-modified variables.
- Prefer `switch` over multiple `if` statements and avoid `goto`.
- Use `malloc`, `calloc`, `realloc`, and `free` for memory management.
- Follow `snake_case` for variables/functions and `UPPER_CASE` for macros.

C coding preferences:
- Use X-Macro patterns for repetitive code generation.
- Use `assert` for checks, `errno` for errors, and `strdup` for strings.
- Prefer functional programming principles and use `inline` for performance-critical functions.

Safe C coding standards:
- Use static analyzers, avoid undefined behavior, and adhere to the C standard.
- Use thread-safe practices and validate inputs.
- Enable compiler warnings (`-Wall`, `-Wextra`, `-Werror`) and use memory sanitizers.

Hardware-specific practices:
- Use hardware abstraction layers and avoid hardcoding hardware addresses.
- Keep ISRs minimal and ensure atomicity for shared resources.

Code review and testing:
- Conduct peer reviews, write unit/integration tests, and use code coverage tools.
- Perform stress and boundary testing for hardware-specific code.

Documentation and maintainability:
- Document hardware-specific details and maintain a changelog.
- Use CI/CD practices and containerization for consistent environments.

suggestions:
- Suggest libraries for embedded C.
- Suggest approaches to improve code quality.

environments:
- Use WSL Ubuntu environment.
- Run tests in Docker containers.
- Use CMake for builds, cross-compilation, and testing.
- Use Ceedling, CMock, Google Test, and Google Mock for testing.
- Use gdb, Valgrind, AddressSanitizer, and static analyzers for debugging.
- Use gcov for coverage and Doxygen for documentation.

Source Code Control:
- Use Git and GitHub for version control.
- Use SonarQube for code quality.
- Use GitHub Actions for CI/CD.

C coding patterns:
1. 
```C
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

/* X-Macro Core Definition */
#define PARAMETER_LIST                                                                                                 \
    X(FIRST_ITEM, int, sizeof(int), firstItem, "FIRST_Item_name", (0x00 << 1 | 0x00 << 2), "FIRST_ITEM_LABEL")         \
    X(SECOND_ITEM, float, sizeof(float), secondItem, "SECOND_Item_name", (0x01 << 1 | 0x00 << 2), "SECOND_ITEM_LABEL") \
    X(THIRD_ITEM, char, sizeof(char), thirdItem, "THIRD_Item_name", (0x00 << 1 | 0x01 << 2), "THIRD_ITEM_LABEL")

/* Enum Generation */
#define X(index, type, size, name, alias, access, label) index,
typedef enum
{
    PARAMETER_LIST
        MAX_ITEMs
} param_index_t;
#undef X

/* Application Database Structure */
typedef struct app_db_t
{
#define X(index, type, size, name, alias, access, label) type name;
    PARAMETER_LIST
#undef X
} app_db;

/* Parameter Metadata Structure */
typedef struct
{
    unsigned short index;
    size_t size;
    unsigned char access;
    const char *alias;
    const char *label;
} param_metadata;

/* Metadata Initialization */
#define X(index, type, size, name, alias, access, label) \
    {index, size, access, alias, label},
param_metadata parameters_metadata[MAX_ITEMs] = {
    PARAMETER_LIST};
#undef X

/* Initialization Functions */
void init_app_db(app_db *db)
{
/* Initialize DB */
#define X(index, type, size, name, alias, access, label) \
    memset(&db->name, 0, size);
    PARAMETER_LIST
#undef X
    /* Load DB from Persistency */
    // reads from a file with noraml file operations
    // but the file name is located at /home/rrh/cfg/db/rrh_app.db
    // and loads the data into the db structure
}

/* Debug Output */
void print_metadata()
{
    for (int i = 0; i < MAX_ITEMs; i++)
    {
        printf("Index: %d,\t Size: %zu,\t Access: 0x%02X\t"
               "Alias: %s\tLabel: %s\n",
               parameters_metadata[i].index,
               parameters_metadata[i].size,
               parameters_metadata[i].access,
               parameters_metadata[i].alias,
               parameters_metadata[i].label);
    }
}

int main()
{
    app_db my_db;
    init_app_db(&my_db);

    // Example usage
    my_db.firstItem = 42;
    my_db.secondItem = 3.14f;
    my_db.thirdItem = 'A';

    print_metadata();

    printf("\n");
    printf("DB Values:\n"
           "firstItem: %d\n"
           "secondItem: %.2f\n"
           "thirdItem: %c\n",
           my_db.firstItem,
           my_db.secondItem,
           my_db.thirdItem);

    return 0;
}
```