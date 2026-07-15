# learning-spring-boot-concepts

This repository is now configured as a Gradle-based Spring Boot project.

## Project layout

- `build.gradle` - Gradle build definition
- `settings.gradle` - Gradle root project name
- `gradlew` / `gradlew.bat` - Gradle wrapper scripts
- `gradle/wrapper/` - Gradle wrapper configuration
- `src/main/java/` - application source code
- `src/test/java/` - test source code

## Requirements

- Java 21+

## Build

```powershell
.\gradlew.bat build
```

## Run tests

```powershell
.\gradlew.bat test
```

## Run the application

```powershell
.\gradlew.bat bootRun
```

