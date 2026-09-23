<p align="center">
   <img src="./front/src/favicon.png" width="192px" />
</p>

# MicroCRM (P7 - Développeur Full-Stack - Java et Angular - Mettez en œuvre l'intégration et le déploiement continu d'une application Full-Stack)

MicroCRM est une application de démonstration basique ayant pour être objectif de servir de socle pour le module "P7 - Développeur Full-Stack".

L'application MicroCRM est une implémentation simplifiée d'un ["CRM" (Customer Relationship Management)](https://fr.wikipedia.org/wiki/Gestion_de_la_relation_client). Les fonctionnalités sont limitées à la création, édition et la visualisations des individus liés à des organisations.

![Page d'accueil](./misc/screenshots/screenshot_1.png)
![Édition de la fiche d'un individu](./misc/screenshots/screenshot_2.png)

## Code source

### Organisation

Ce [monorepo](https://en.wikipedia.org/wiki/Monorepo) contient les 2 composantes du projet "MicroCRM":

- La partie serveur (ou "backend"), en Java SpringBoot 3;
- La partie cliente (ou "frontend"), en Angular 17.

### Démarrer avec les sources

#### Serveur

##### Dépendances

- [OpenJDK >= 17](https://openjdk.org/)

##### Procédure

1. Se positionner dans le répertoire `back` avec une invite de commande:

   ```shell
   cd back
   ```

2. Construire le JAR:

   ```shell
   # Sur Linux
   ./gradlew build

   # Sur Windows
   gradlew.bat build
   ```

3. Démarrer le service:

   ```shell
   java -jar build/libs/microcrm-0.0.1-SNAPSHOT.jar
   ```

Puis ouvrir l'URL http://localhost:8080 dans votre navigateur.

#### Client

##### Dépendances

- [NPM >= 10.2.4](https://www.npmjs.com/)

##### Procédure

1. Se positionner dans le répertoire `front` avec une invite de commande:

   ```shell
   cd front
   ```

2. (La première fois seulement) Installer les dépendances NodeJS:

   ```shell
   npm install
   ```

3. Démarrer le service de développement:

   ```shell
   npx @angular/cli serve
   ```

Puis ouvrir l'URL http://localhost:4200 dans votre navigateur.

### Exécution des tests

#### Client

**Dépendances**

- Google Chrome ou Chromium

Dans votre terminal:

```shell
cd front
npm ci
npm test -- --watch=false --browsers=ChromeHeadless --code-coverage
```

Cette commande execute les tests Angular sans interface graphique et genere:

- le rapport JUnit dans `front/test-results/junit.xml`;
- le rapport de couverture HTML dans `front/coverage/microcrm/index.html`;
- le rapport LCOV dans `front/coverage/microcrm/lcov.info`;
- un resume de couverture dans la sortie du terminal.

#### Serveur

Dans votre terminal:

```shell
cd back
./gradlew test
```

Pour construire le backend et generer les rapports de tests et de couverture JaCoCo:

```shell
./gradlew clean build jacocoTestReport
```

Les resultats sont disponibles dans:

- `back/build/test-results/test/` pour les resultats JUnit XML;
- `back/build/reports/jacoco/test/jacocoTestReport.xml` pour SonarCloud;
- `back/build/reports/jacoco/test/html/index.html` pour le rapport HTML.

### Images Docker

#### Client

##### Construire l'image

```shell
docker build -f front/Dockerfile -t orion-microcrm-front:latest front
```

##### Exécuter l'image

```shell
docker run -it --rm -p 80:8080 orion-microcrm-front:latest
```

L'application sera disponible sur http://localhost.

#### Serveur

##### Construire l'image

```shell
docker build -f back/Dockerfile -t orion-microcrm-back:latest back
```

##### Exécuter l'image

```shell
docker run -it --rm -p 8080:8080 orion-microcrm-back:latest
```

L'API sera disponible sur http://localhost:8080.
