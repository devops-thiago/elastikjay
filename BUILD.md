# Build Instructions

This project has been migrated to Java 17 and includes modern build tooling for code quality and testing.

## Prerequisites

- Java 17 or later
- Maven 3.6+

## Build Commands

### Basic Build
```bash
mvn clean compile
```

### Run Tests
```bash
mvn test
```

### Full Build with Verification
```bash
mvn clean verify
```

### Code Quality Checks

#### Static Analysis (SpotBugs)
```bash
mvn spotbugs:check
```

#### Code Style Checks (Checkstyle)
```bash
mvn checkstyle:check
```

#### Test Coverage Report (JaCoCo)
Test coverage reports are generated automatically during the test phase and can be found in:
- `core/target/site/jacoco/index.html`

### Complete Quality Check
```bash
mvn clean verify spotbugs:check checkstyle:check
```

## Dependencies Updated

- **Java**: Upgraded from 1.7 to 17
- **log4j**: Upgraded from 1.2.17 to 2.20.0 (security fix)
- **JUnit**: Upgraded from 4.11 to 4.13.2
- **Gson**: Upgraded from 2.2.1 to 2.10.1

## Code Quality Tools Added

- **SpotBugs**: Static analysis for bug detection
- **Checkstyle**: Code style enforcement (Google Java Style)
- **JaCoCo**: Test coverage reporting

## Notes

- No javax to Jakarta migration was needed (no javax dependencies found)
- Elasticsearch version was kept at 1.2.1 to maintain API compatibility
- SpotBugs is configured to only fail on High priority issues to maintain build stability
- Checkstyle is configured to report but not fail the build