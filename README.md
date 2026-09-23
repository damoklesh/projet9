<p align="center">
   <img src="./front/src/favicon.png" width="192px" />
</p>

# MicroCRM

MicroCRM is a basic demonstration application used as a foundation for the P7 Full-Stack Developer module.

It is a simplified [Customer Relationship Management (CRM)](https://en.wikipedia.org/wiki/Customer_relationship_management) application for creating and editing people associated with organizations.

![Home page](./misc/screenshots/screenshot_1.png)
![Person details editing](./misc/screenshots/screenshot_2.png)

## Source code

This [monorepo](https://en.wikipedia.org/wiki/Monorepo) contains two components:

- A Java Spring Boot 3 backend;
- An Angular 17 frontend.

## Local development

### Backend

Requirements:

- [OpenJDK 17 or later](https://openjdk.org/)

Build and test the backend:

```shell
cd back
./gradlew build
./gradlew test
```

Start the backend after building:

```shell
java -jar build/libs/microcrm-0.0.1-SNAPSHOT.jar
```

The API is available at http://localhost:8080.

### Frontend

Requirements:

- [NPM 10.2.4 or later](https://www.npmjs.com/)

Install, build, and start the Angular development server:

```shell
cd front
npm ci
npm run build
npx @angular/cli serve
```

The development application is available at http://localhost:4200.

## Tests and coverage

### Backend

```shell
cd back
./gradlew test
./gradlew clean build jacocoTestReport
```

- JUnit Platform runs the Java tests.
- JUnit XML results are written to `back/build/test-results/test/`.
- JaCoCo XML coverage for SonarCloud is written to `back/build/reports/jacoco/test/jacocoTestReport.xml`.
- JaCoCo HTML coverage is written to `back/build/reports/jacoco/test/html/index.html`.

### Frontend

The Angular tests use Jasmine and Karma. Chrome or Chromium is required.

```shell
cd front
npm ci
npm test -- --watch=false --browsers=ChromeHeadless --code-coverage
```

This command runs headlessly and produces:

- JUnit XML results in `front/test-results/junit.xml`;
- HTML coverage in `front/coverage/microcrm/index.html`;
- an LCOV report for SonarCloud in `front/coverage/microcrm/lcov.info`;
- a text coverage summary in the terminal.

## Docker

The frontend and backend use independent multi-stage Dockerfiles. The runtime images do not use Supervisor or the former standalone container.

### Build the individual images

```shell
docker build -f front/Dockerfile -t orion-microcrm-front:latest front
docker build -f back/Dockerfile -t orion-microcrm-back:latest back
```

Run the images independently:

```shell
docker run -it --rm -p 80:8080 orion-microcrm-front:latest
docker run -it --rm -p 8080:8080 orion-microcrm-back:latest
```

The frontend is available at http://localhost and the API at http://localhost:8080.

### Docker Compose

The Compose file starts the `front` and `back` services. HSQLDB remains embedded in the backend; no external database is required.

From the repository root, start the complete application with:

```shell
docker compose up --build
```

The first run builds both images. Once the containers are running, open the frontend at:

```text
http://localhost
```

The backend API is available at:

```text
http://localhost:8080
```

The frontend container listens on port 8080 internally and is published on port 80 by Compose. The backend is published on port 8080. The frontend uses `http://localhost:8080` to call the backend.

Stop the services with:

```shell
docker compose down
```

## Local ELK monitoring stack

The local Elasticsearch, Logstash, and Kibana stack is kept separate from the MicroCRM runtime Compose file. It is intended for local observability and is not started by CI/CD.

The stack requires approximately 4 GB of RAM available to Docker.

Start the stack from the repository root:

```shell
docker compose -f monitoring/docker-compose-elk.yml up
```

Services:

- Elasticsearch: http://localhost:9200
- Logstash TCP JSON input: `localhost:5000`
- Kibana: http://localhost:5601

The Logstash pipeline accepts newline-delimited JSON over TCP and writes events to daily `microcrm-*` Elasticsearch indices. Stop the stack with:

```shell
docker compose -f monitoring/docker-compose-elk.yml down
```

Add `-v` to the `down` command only when the local Elasticsearch data volume should also be removed.

## CI/CD

The workflows follow this sequence:

```text
push / pull_request
        ↓
build
        ↓
tests + coverage
        ↓
SonarCloud
        ↓
Quality Gate
        ↓
Docker
        ↓
main → GHCR
```

The CI workflow validates pushes, pull requests, and manual runs. The CD workflow publishes images only after a successful CI workflow caused by a push to `main`. Pull requests never publish images.

Published image tags are:

- `ghcr.io/damoklesh/orion-microcrm-front:sha-<commit>` and `:main`;
- `ghcr.io/damoklesh/orion-microcrm-back:sha-<commit>` and `:main`.

The `sha-<commit>` tag provides immutable traceability to the validated commit.

## Secrets

The only repository secret required for SonarCloud analysis is:

```text
SONAR_TOKEN
```

Configure it in GitHub Actions repository secrets. Never commit its value, place it in a Dockerfile, or print it in logs. GHCR authentication uses the automatic GitHub Actions `GITHUB_TOKEN`.
