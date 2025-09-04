# ElastiKJay

[![Build Status](https://github.com/devops-thiago/elastikjay/workflows/CI/badge.svg)](https://github.com/devops-thiago/elastikjay/actions)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=devops-thiago_elastikjay&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=devops-thiago_elastikjay)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=devops-thiago_elastikjay&metric=coverage)](https://sonarcloud.io/summary/new_code?id=devops-thiago_elastikjay)
[![codecov](https://codecov.io/gh/devops-thiago/elastikjay/branch/main/graph/badge.svg)](https://codecov.io/gh/devops-thiago/elastikjay)
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=devops-thiago_elastikjay&metric=sqale_rating)](https://sonarcloud.io/summary/new_code?id=devops-thiago_elastikjay)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=devops-thiago_elastikjay&metric=security_rating)](https://sonarcloud.io/summary/new_code?id=devops-thiago_elastikjay)
[![Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=devops-thiago_elastikjay&metric=reliability_rating)](https://sonarcloud.io/summary/new_code?id=devops-thiago_elastikjay)
[![Maven Central](https://img.shields.io/maven-central/v/com.arquivolivre/elastikjay.svg?label=Maven%20Central)](https://search.maven.org/search?q=g:%22com.arquivolivre%22%20AND%20a:%22elastikjay%22)
[![License](https://img.shields.io/github/license/devops-thiago/elastikjay.svg)](LICENSE)

ElastiKJay is a Java library that provides annotation-based object mapping and simplified interaction with Elasticsearch. It allows developers to easily map Java POJOs to Elasticsearch indices using annotations, similar to how JPA works with relational databases.

## Features

- **Annotation-driven mapping**: Use simple annotations to define how your Java objects map to Elasticsearch indices
- **Simplified CRUD operations**: Easy-to-use API for creating, reading, updating, and deleting documents
- **Bulk operations**: High-performance bulk insert and update operations
- **Index management**: Create, delete, and manage Elasticsearch indices programmatically
- **Flexible configuration**: Support for both single-node and cluster configurations
- **Type safety**: Strongly-typed operations with automatic serialization/deserialization

## Getting Started

### Prerequisites

- Java 17 or later
- Maven 3.6+
- Elasticsearch 1.2.1+ (compatible)

### Installation

Add ElastiKJay to your Maven project:

```xml
<dependency>
    <groupId>com.arquivolivre</groupId>
    <artifactId>elastikjay</artifactId>
    <version>1.2.1-SNAPSHOT</version>
</dependency>
```

## Usage Examples

### 1. Defining Elasticsearch Mappings with Annotations

Use annotations to define how your Java classes map to Elasticsearch:

```java
import com.arquivolivre.elastikjay.annotations.*;

@Index(name = "products", type = "product")
public class Product {
    
    private String id;
    
    @Analyzer("standard")
    private String name;
    
    @NotAnalyzed
    private String category;
    
    @NotIndexed
    private String internalNotes;
    
    private double price;
    
    @Nested
    private List<Review> reviews;
    
    // Getters and setters...
}

public class Review {
    private String author;
    private int rating;
    private String comment;
    
    // Getters and setters...
}
```

### 2. Configuring Elasticsearch Connection

Create a properties file `es.properties` in your resources:

```properties
# Single node configuration
elasticsearch.node=localhost
elasticsearch.port=9300

# Or cluster configuration
elasticsearch.cluster.name=my-cluster
elasticsearch.cluster.nodes=node1,node2,node3
elasticsearch.cluster.ports=9300,9301,9302
```

### 3. Basic CRUD Operations

```java
import com.arquivolivre.elastikjay.commons.IndexManager;
import com.arquivolivre.elastikjay.commons.IndexManagerImpl;
import com.arquivolivre.elastikjay.config.Configuration;

// Initialize the IndexManager
Configuration config = new Configuration();
IndexManager indexManager = new IndexManagerImpl(config.elasticSearchClient());

// Create an index for your class
Product sampleProduct = new Product();
indexManager.createIndex("products", "product", sampleProduct);

// Insert a document
Product product = new Product();
product.setId("1");
product.setName("Laptop Computer");
product.setCategory("electronics");
product.setPrice(999.99);

indexManager.addToBulk("1", product);
indexManager.executeBulkAdd();

// Retrieve a document
Product retrievedProduct = indexManager.get("1", Product.class);
System.out.println("Product: " + retrievedProduct.getName());

// Check if index exists
boolean exists = indexManager.indexExists("products");
```

### 4. Bulk Operations for Performance

```java
// Bulk insert multiple documents
List<Product> products = Arrays.asList(
    new Product("1", "Laptop", "electronics", 999.99),
    new Product("2", "Mouse", "electronics", 29.99),
    new Product("3", "Keyboard", "electronics", 79.99)
);

for (Product product : products) {
    indexManager.addToBulk(product.getId(), product);
}

// Execute all bulk operations at once
indexManager.executeBulkAdd();
```

### 5. Index Management

```java
// Create index with custom settings
indexManager.createIndex("products", "product", new Product());

// Delete an index
indexManager.deleteIndex("products");

// Delete multiple indices
indexManager.deleteIndices("products", "orders", "customers");

// Check if index exists
if (!indexManager.indexExists("products")) {
    indexManager.createIndex("products", "product", new Product());
}
```

## Available Annotations

| Annotation | Description |
|------------|-------------|
| `@Index` | Defines the Elasticsearch index name and type for a class |
| `@Analyzer` | Specifies the analyzer to use for text analysis |
| `@NotAnalyzed` | Marks a field as not analyzed (exact match only) |
| `@NotIndexed` | Excludes a field from being indexed |
| `@Ignored` | Completely ignores a field during mapping |
| `@Nested` | Defines nested object mapping for complex types |
| `@Analysis` | Configures analysis settings for the field |
| `@Filter` | Applies filters during analysis |

## Configuration Options

ElastiKJay supports flexible configuration through properties:

### Single Node Setup
```properties
elasticsearch.node=localhost
elasticsearch.port=9300
```

### Cluster Setup
```properties
elasticsearch.cluster.name=production-cluster
elasticsearch.cluster.nodes=es-node1,es-node2,es-node3
elasticsearch.cluster.ports=9300,9300,9300
```

## Building and Development

### CI/CD Pipeline

This project uses GitHub Actions for continuous integration and deployment:

- **CI Pipeline**: Runs on every push to `main` branch and weekly schedule
  - Builds the project with Maven
  - Runs all tests with coverage reporting
  - Performs static analysis (SpotBugs, Checkstyle)
  - Scans code quality with SonarCloud
  - Uploads coverage reports to Codecov

- **PR Checks**: Runs on every pull request
  - Same quality checks as CI
  - Posts quality report comments on PRs
  - Ensures all checks pass before merge

### Build Commands

```bash
# Clean and compile
mvn clean compile

# Run tests
mvn test

# Full build with verification
mvn clean verify

# Run code quality checks
mvn spotbugs:check checkstyle:check

# Generate test coverage report
mvn test
# Report available at: core/target/site/jacoco/index.html
```

### Code Quality Tools

This project uses several tools to maintain code quality:

- **SpotBugs**: Static analysis for bug detection
- **Checkstyle**: Code style enforcement (Google Java Style)
- **JaCoCo**: Test coverage reporting

### Dependencies

- **Java**: 17 (upgraded from 1.7)
- **Elasticsearch**: 1.2.1
- **Log4j**: 2.20.0 (security update)
- **Gson**: 2.10.1
- **JUnit**: 4.13.2

## License

This project is licensed under the terms specified in the LICENSE file.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests and code quality checks
5. Submit a pull request

## Author

Thiago da Silva Gonzaga <thiagosg@sjrp.unesp.br>
