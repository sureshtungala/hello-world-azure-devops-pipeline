<!-- copilot-instructions.md: concise, actionable guidance for AI coding agents -->
# Project snapshot

This is a small Spring Boot demo service (Java 8, Spring Boot 2.1.7.RELEASE) with a single REST controller and simple CI/CD artefacts. The main app class is at `src/main/java/com/in28minutes/rest/webservices/restfulwebservices/RestfulWebServicesApplication.java` and the example controller is `src/main/java/com/in28minutes/rest/webservices/restfulwebservices/HelloWorldController.java`.

# Big picture

- Purpose: lightweight demo API that returns a JSON string at `/`.
- Runtime: Java 8; packaged as a JAR via Maven (`pom.xml`).
- Port: configured to `5000` in `src/main/resources/application.properties` and in the `Dockerfile`.

# Key files to inspect (examples)

- `pom.xml` — Maven build, Spring Boot parent, `spring-boot-maven-plugin` used for packaging.
- `src/main/java/.../RestfulWebServicesApplication.java` — Spring Boot entrypoint.
- `src/main/java/.../HelloWorldController.java` — simple GET handler that returns a JSON string.
- `src/main/resources/application.properties` — server port and logging level (`server.port = 5000`).
- `Dockerfile` — multi-stage image: builds with Maven image, then uses OpenJDK alpine and copies `target/hello-world-java.jar` into the image; exposes `5000`.
- `01-first-azure-pipelines.yml` — Azure Pipelines CI example (check for pipeline steps before altering CI behavior).
- `Jenkinfile` — placeholder content; do not rely on it as authoritative.

# Build / run / test (commands to run locally)

- Build (including tests): `mvn clean package`
- Build without tests: `mvn clean package -DskipTests`
- Run from sources: `mvn spring-boot:run` (uses `application.properties` port 5000)
- Run packaged JAR: `java -jar target/hello-world-java.jar` (Dockerfile expects `/target/hello-world-java.jar`)
- Run tests: `mvn test`

# CI & Docker notes

- Docker: multi-stage build compiles the project inside `maven:3.8.2-jdk-8-slim` and produces a runtime image based on `openjdk:8-jdk-alpine` that copies `hello-world-java.jar` to `/hello-world-java.jar` and runs it. Keep the `COPY --from=stage1 /home/app/target/hello-world-java.jar hello-world-java.jar` path in sync with `pom.xml` output name.
- CI: prefer `01-first-azure-pipelines.yml` for pipeline behavior; `Jenkinfile` currently contains placeholder text — inspect it before using.

# Code patterns & conventions (project-specific)

- Single-package demo project under `com.in28minutes.rest.webservices.restfulwebservices` — keep new classes in that package unless adding a new module.
- Controller responses sometimes return raw JSON strings (see `HelloWorldController#helloWorld`) — when extending, prefer returning typed DTOs annotated with `@RestController` and using Spring's content negotiation.
- Tests use JUnit 4 with `SpringRunner` and `@SpringBootTest` in `src/test/java/.../RestfulWebServicesApplicationTests.java`.

# Guidance for an AI coding agent

- Read and preserve `pom.xml` Java version and final artifact name (`<finalName>hello-world-java</finalName>`). If changing packaging, update `Dockerfile` accordingly.
- When changing the HTTP port, update `src/main/resources/application.properties` and `Dockerfile`'s `EXPOSE` line.
- Do not assume the `Jenkinfile` is usable; prefer `01-first-azure-pipelines.yml` for CI details.
- When adding new endpoints, include unit/integration tests under `src/test/java` following the existing `@SpringBootTest` pattern.
- Keep logging configuration changes minimal; current default is `logging.level.org.springframework = debug`.

# When in doubt / quick checks

- To verify locally: `mvn clean package && java -jar target/hello-world-java.jar` then curl `http://localhost:5000/`.
- To build the Docker image locally (from repository root):

```bash
docker build -t hello-world-java:local .
docker run -p 5000:5000 hello-world-java:local
```

If anything here is incorrect or you want additional, more detailed agent rules (linting, commit message style, or branching), tell me what to include and I'll iterate.
