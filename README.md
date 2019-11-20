# light-bom
Light platform BOM repository.

# Goals
BOM files are an important part of platforms because they define a set of dependency versions 
that are expected to be compatible. It's possible to override dependency versions defined in 
a BOM, but isn't normally recommended (know what you're doing if you try this). Another important 
benefit of a BOM is that it ensures that a cluster of applications utilize the same dependency 
versions, which can prevent [dependency hell](https://en.wikipedia.org/wiki/Dependency_hell) 
situations.

# Flavors
Depending on your needs there should be a BOM file that works for your intended use case.

- light-platform-bom - This BOM file specifies the base dependencies that are probably necessary for 
all Light projects. Most other BOM files are built off of this one.
- light-rest-bom - This BOM file specifies the dependencies that are probably necessary for Light 
Rest projects.
- light-graphql-bom - This BOM file specifies the dependencies that are probably necessary for Light 
GraphQL projects.
- light-test-bom - This BOM file specifies the dependencies that can be used to test Light projects. 
This probably is only necessary for in-house projects. It does not depend on the light-platform-bom.

# Usage

## Maven
It's recommended that the dependency management section from our BOM files are imported, but they 
can also be used as parent POM files.

```xml
<project xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://maven.apache.org/POM/4.0.0"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.networknt</groupId>
    <artifactId>some-project</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
	
    <properties>
        <version.light>2.0.8-SNAPSHOT</version.light>
	</properties>
	
	<dependencyManagement>
        <dependencies>
            <!-- This is how to override dependency versions imported from the BOM. -->
            <dependency>
                <groupId>com.networknt</groupId>
                <artifactId>audit</artifactId>
                <version>1.6.0-SNAPSHOT</version>
			</dependency>
			<!-- Import dependency management from Light Platform BOM. -->
            <dependency>
                <groupId>com.networknt</groupId>
                <artifactId>light-platform-bom</artifactId>
                <version>${version.light}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
	
	<!-- Versions should be omitted so that the dependency management section handles them. -->
	<dependencies>
        <dependency>
            <groupId>com.networknt</groupId>
            <artifactId>audit</artifactId>
        </dependency>
        <dependency>
            <groupId>com.networknt</groupId>
            <artifactId>config</artifactId>
        </dependency>
	</dependencies>
</project>
```

## Gradle
Gradle partially supports importing BOM files now, but at the time of investigation (around the 
release of Gradle 5.0) behavior was different than that of Maven BOM imports. It is recommended 
to utilize the io.spring.dependency-management plugin for importing the BOM because it was 
designed to follow the same behavior as Maven BOM imports.

```groovy
buildscript {
  repositories {
    jcenter()
  }
  dependencies {
    classpath "io.spring.gradle:dependency-management-plugin:0.5.1.RELEASE"
  }
}

apply plugin: "io.spring.dependency-management"

dependencyManagement {
  // This is how to override dependency versions imported from the BOM.
  dependencies {
    dependency 'com.networknt:audit:1.6.0-SNAPSHOT'
  }
	
  imports {
    mavenBom 'com.networknt:light-platform-bom:2.0.8-SNAPSHOT'
  }
}

dependencies {
    // Versions should be omitted so that they handled by the resolution 
	// strategies setup by the dependency management plugin.
    compile 'com.networknt:audit'
    compile 'com.networknt:config'
}
```