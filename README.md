# Maven Multi-Module Project Structure – rapido

## Overview

### Projects

```

rapido-web
(customer rent bike)
|- Controller [presentation-tier]
|- Service
|- Dao
|- Bo
|- Entities

rapido-admin
(back-office to manage administration activities)
|- Controller [presentation-tier]
|- Service
|- Dao
|- Bo
|- Entities

```

### Common Observations

1. The technology used in both projects is the same:
   - Same dependency configurations
   - Same plugin configurations

2. Across these projects, the **business and persistence components** and functionality are shared/common.

---

### Problem

If we create these as two different projects, it leads to:

1. Duplication of Maven project configurations (dependencies and plugins)
2. Duplication of business and persistence-tier components

---

## Solution – Introducing a Shared Library Project

```

rapido-service
[Library project]
|- Service
|- Dao
|- Bo
|- Entities

```

- `rapido-web` and `rapido-admin` can include `rapido-service` as a dependency.

---

### Final Project Structure

```

rapido/
├── pom.xml
├── rapido-service/
│   ├── pom.xml
│   └── src/main/java
├── rapido-web/
│   ├── pom.xml
│   └── src/main/java
└── rapido-admin/
├── pom.xml
└── src/main/java

```

---

### Benefits

1. Maven project configurations (dependencies and plugins) are centralized and not duplicated.
2. Establishes a **build order dependency**:
   - Build `rapido-service` first.
   - Install its artifact into the local Maven repository.
   - Include it as a dependency in `rapido-web` and `rapido-admin`.

---

## Build Process

Always navigate to the **parent project (`rapido`)** and run the build.
This ensures Maven automatically builds all three subprojects based on their module order.

---

## Parent Project Structure (`rapido`)

```

rapido (parent)
├── pom.xml
│   ├── <packaging>pom</packaging>
│   ├── <dependencyManagement> (config)
│   │   ├── <dependency> (groupId, artifactId, version)
│   ├── <build>
│   │   └── <pluginManagement>
│   │       ├── <plugin> (groupId, artifactId, version)
│   └── <modules>
│       ├── rapido-service
│       ├── rapido-web
│       └── rapido-admin

```

---

## rapido-service (Library Module)

```

src/main/java
pom.xml (packaging: jar) <parent> rapido </parent> <dependencies> (groupId, artifactId)

```

---

## rapido-web

```

src/main/java
pom.xml (packaging: war) <parent> rapido </parent> <dependencies> <dependency> rapido-service </dependency> </dependencies>

```

---

## rapido-admin

```

src/main/java
pom.xml (packaging: war) <parent> rapido </parent> <dependencies> <dependency> rapido-service </dependency> </dependencies>

```

---

## Maven Central Reference

```

maven-central-repo
└── spring-boot-starter-parent (parent)
├── pom.xml
├── <packaging>pom</packaging>
├── <dependencyManagement> (config)
│   ├── <dependencies>
│   │   └── <dependency> (groupId, artifactId, version)
├── <build>
│   └── <pluginManagement>
│       ├── <plugin> (groupId, artifactId, version)
│       └── <plugin> (groupId, artifactId, version)

```

---

### Summary

- `rapido-service`: common business & persistence layer (shared by all)
- `rapido-web`: presentation tier for customer-facing operations
- `rapido-admin`: presentation tier for back-office operations
- Parent POM (`rapido`): manages dependency and plugin versions across modules
- Build always starts from the **parent module** to ensure proper dependency resolution.

---

# DependencyManagement & PluginManagement

`<dependencyManagement>` and `<pluginManagement>` hold configuration details of dependencies and plugins that are **used in the current project** or **inherited by child modules**.

- **dependencyManagement** → Defines versions and configurations of dependencies that can be shared across multiple modules.
- **pluginManagement** → Defines versions and configurations of plugins that can be reused or overridden by child projects.
  

# Pictorial Respentation for Maven Multi-Module Project Structure – rapido

<img width="1752" height="2000" alt="20250518_MULTI_MAVEN_PROJ" src="https://github.com/user-attachments/assets/fce98195-7d87-4c87-8a31-9e6fcdbf8e10" />

# Command for generate maven project in command prompt 

mvn archetype:generate -DgroupId=boot -DartifactId=boot-init -Dversion=1.0.0-SNAPSHOT -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.5
