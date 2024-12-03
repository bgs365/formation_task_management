# Formation Java - Application de Gestion de Tâches

## Répartition du Temps :

- **Semaine 1** : Formation sur Git et Développement d'une Application Java Console.
- **Semaines 2-3** : Introduction à Maven et Formation approfondie en Spring Boot.
- **Semaine 4** : Transformation en Application REST avec Spring Boot.
- **Semaine 5** : Formation en React.
- **Semaine 6** : Développement du Frontend et Intégration avec le Backend.

---

## Introduction

Cette formation a pour but d'apprendre aux développeurs juniors à se familiariser avec l'environnement Java. Nous allons créer une application de gestion de tâches en plusieurs étapes, en commençant par une application console Java simple, puis en évoluant vers une application web complète avec un frontend en React.

---

## Semaine 1 : Formation sur Git et Développement d'une Application Java Console

### Objectifs :

- **Git** : Comprendre les bases de Git pour la gestion de versions et la collaboration.
- **Java Console Application** : Créer une application console en Java permettant de créer, modifier et ajouter des tâches.
- **Docker et IntelliJ IDEA** : Installer et configurer les outils nécessaires.
- **Connexion à une Base de Données** : Configurer PostgreSQL avec Docker et se connecter depuis IntelliJ IDEA.

### Étapes :

#### 1. Installation des Outils

- **Docker** : [Télécharger Docker Desktop](https://www.docker.com/products/docker-desktop)
  - Suivez les instructions d'installation pour votre système d'exploitation.
- **IntelliJ IDEA** : [Télécharger IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/)
  - Choisissez la version Community Edition, qui est gratuite.
- **Git** : [Télécharger Git](https://git-scm.com/downloads)
  - Installez Git sur votre machine pour la gestion de versions.

#### 2. Formation sur Git

- **Documentation Officielle** : [Pro Git Book (français)](https://git-scm.com/book/fr/v2)
- **Tutoriels** :
  - [Apprendre Git en 15 minutes](https://try.github.io/levels/1/challenges/1)
  - [Guide de GitHub](https://guides.github.com/introduction/git-handbook/)

#### 3. Configuration de la Base de Données PostgreSQL avec Docker

- **Créer le fichier `docker-compose.yaml`** :
  - Dans votre répertoire de projet, créez un fichier nommé `docker-compose.yaml` avec le contenu suivant :

    ```yaml
    version: '1'

    services:

      starter-db:
        image: 'postgres:13.1-alpine'
        container_name: task-db
        restart: always
        environment:
          - POSTGRES_USER=user
          - POSTGRES_PASSWORD=password
          - POSTGRES_DB=task
        ports:
          - '5432:5432'
    ```

- **Lancer la base de données** :
  - Ouvrez un terminal dans le dossier contenant `docker-compose.yaml`.
  - Exécutez la commande :

    ```bash
    docker compose up
    ```

  - Cette commande télécharge l'image PostgreSQL et lance un conteneur avec la base de données configurée.

#### 4. Connexion à la Base de Données depuis IntelliJ IDEA

- **Ouvrir IntelliJ IDEA**.
- **Ajouter une source de données** :
  - Allez dans **View > Tool Windows > Database**.
  - Cliquez sur le bouton **+** et sélectionnez **Data Source > PostgreSQL**.
- **Configurer la connexion** :
  - **Host** : `localhost`
  - **Port** : `5432`
  - **Database** : `task`
  - **User** : `user`
  - **Password** : `password`
- **Télécharger le pilote JDBC si demandé** :
  - IntelliJ IDEA proposera de télécharger le pilote JDBC PostgreSQL.
  - Cliquez sur **Download Driver**.

#### 5. Exécution du Script SQL pour Créer les Tables

- **Ouvrir la console SQL** :
  - Dans l'onglet **Database**, faites un clic droit sur la connexion `task` et sélectionnez **New > Query Console**.
- **Exécuter le script SQL** :
  - Copiez et collez le script SQL suivant dans la console :

    ```sql
    -- Création de la table Statuts
    CREATE TABLE Statuts (
        id SERIAL PRIMARY KEY,
        description VARCHAR(50) NOT NULL
    );

    -- Création de la table Utilisateurs
    CREATE TABLE Utilisateurs (
        id SERIAL PRIMARY KEY,
        nom VARCHAR(100) NOT NULL,
        email VARCHAR(100) UNIQUE NOT NULL
    );

    -- Création de la table Catégories
    CREATE TABLE Categories (
        id SERIAL PRIMARY KEY,
        nom VARCHAR(100) NOT NULL
    );

    -- Création de la table Tâches
    CREATE TABLE Taches (
        id SERIAL PRIMARY KEY,
        titre VARCHAR(200) NOT NULL,
        description TEXT,
        date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        date_echeance DATE,
        priorite INTEGER CHECK (priorite BETWEEN 1 AND 5),
        statut_id INTEGER,
        categorie_id INTEGER,
        utilisateur_id INTEGER,
        FOREIGN KEY (categorie_id) REFERENCES Categories (id) ON DELETE SET NULL,
        FOREIGN KEY (utilisateur_id) REFERENCES Utilisateurs (id) ON DELETE SET NULL,
        FOREIGN KEY (statut_id) REFERENCES Statuts (id)
    );

    -- Insertion de statuts
    INSERT INTO Statuts (description) VALUES ('Non commencé'), ('En cours'), ('Complété'), ('En attente');

    -- Insertion d'utilisateurs
    INSERT INTO Utilisateurs (nom, email) VALUES 
    ('John Doe', 'johndoe@example.com'),
    ('Jane Smith', 'janesmith@example.com');

    -- Insertion de catégories
    INSERT INTO Categories (nom) VALUES 
    ('Développement'), 
    ('Conception');

    -- Insertion de tâches
    INSERT INTO Taches (titre, description, date_echeance, priorite, statut_id, categorie_id, utilisateur_id) 
    VALUES 
    ('Concevoir la base de données', 'Concevoir les schémas nécessaires pour la gestion de projets', '2024-12-31', 3, 1, 2, 1),
    ('Développer l''interface utilisateur', 'Créer les interfaces pour l''application de gestion de tâches', '2024-12-31', 4, 2, 1, 2);

    -- Requête pour obtenir toutes les tâches
    SELECT * FROM Taches;
    ```

  - Exécutez le script en cliquant sur le bouton **Run** (icône en forme de triangle vert) ou en appuyant sur **Ctrl+Enter**.

#### 6. Téléchargement du Pilote JDBC PostgreSQL

- **Télécharger le pilote** :
  - Rendez-vous sur le site officiel : [Télécharger le pilote JDBC PostgreSQL](https://jdbc.postgresql.org/download/).
  - Choisissez la version compatible avec Java 8, par exemple **postgresql-42.2.18.jre7.jar**.

#### 7. Ajouter le Pilote JDBC au Projet IntelliJ IDEA

- **Ajouter la bibliothèque au projet** :
  - Faites un clic droit sur votre projet dans IntelliJ IDEA et sélectionnez **Open Module Settings**.
  - Dans l'onglet **Libraries**, cliquez sur le bouton **+** et sélectionnez le fichier JAR du pilote JDBC que vous avez téléchargé.
  - Assurez-vous que la bibliothèque est associée à votre module (cochez la case correspondante).

#### 8. Développement de l'Application Console Java

- **Créer un nouveau projet Java** :
  - Dans IntelliJ IDEA, sélectionnez **File > New > Project**.
  - Choisissez **Java** et configurez le JDK (version 8 ou supérieure).
- **Structure du projet** :
  - Créez un package, par exemple `com.mycompany.taskmanager`.
  - Créez une classe principale `Main.java` dans ce package.
- **Établir la connexion à la base de données** :
  - Utilisez `DriverManager` pour établir la connexion.
  - Exemple de code :

    ```java
    import java.sql.Connection;
    import java.sql.DriverManager;
    import java.sql.SQLException;

    public class Main {
        private static final String URL = "jdbc:postgresql://localhost:5432/task";
        private static final String USER = "user";
        private static final String PASSWORD = "password";

        public static void main(String[] args) {
            try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD)) {
                System.out.println("Connexion établie !");
                // Votre code ici
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    ```

- **Implémenter les fonctionnalités CRUD** :
  - **Créer** une nouvelle tâche :
    - Utilisez un `PreparedStatement` pour insérer une nouvelle ligne dans la table `Taches`.
  - **Modifier** une tâche existante :
    - Mettez à jour les enregistrements en utilisant l'ID de la tâche.
  - **Supprimer** une tâche :
    - Supprimez une tâche en fonction de son ID.
  - **Lister** toutes les tâches :
    - Exécutez une requête `SELECT` pour récupérer et afficher toutes les tâches.

- **Gérer les entrées utilisateur** :
  - Utilisez la classe `Scanner` pour lire les entrées depuis la console.
  - Proposez un menu interactif pour naviguer entre les options.

#### 9. Gestion de Version avec Git

- **Initialiser le dépôt Git** :
  - Dans le terminal, naviguez jusqu'au répertoire de votre projet.
  - Exécutez la commande :

    ```bash
    git init
    ```

- **Créer un fichier `.gitignore`** :
  - Ajoutez les fichiers et dossiers à ignorer, par exemple :

    ```
    /out/
    *.iml
    .idea/
    ```

- **Premier commit** :
  - Ajoutez les fichiers :

    ```bash
    git add .
    ```

  - Faites un commit :

    ```bash
    git commit -m "Initial commit"
    ```

- **Configurer le dépôt distant sur GitHub** :
  - Créez un nouveau dépôt sur GitHub.
  - Associez le dépôt local au dépôt distant :

    ```bash
    git remote add origin https://github.com/votre-utilisateur/votre-depot.git
    ```

  - Poussez les changements :

    ```bash
    git push -u origin master
    ```

---

## Semaines 2-3 : Introduction à Maven et Formation approfondie en Spring Boot

### Objectifs :

- **Maven** :
  - Comprendre le rôle de Maven dans la gestion de projet Java.
  - Convertir le projet existant en un projet Maven.
- **Spring Boot** :
  - Découvrir le framework Spring Boot pour le développement d'applications web.
  - Recréer l'application en utilisant Spring Boot pour faciliter le développement et la gestion des dépendances.

### Étapes :

#### 1. Introduction à Maven

- **Documentation** : [Apache Maven - Getting Started](https://maven.apache.org/guides/getting-started/index.html)
- **Installer Maven** :
  - [Télécharger Maven](https://maven.apache.org/download.cgi)
  - Configurer les variables d'environnement (`MAVEN_HOME` et `PATH`).

#### 2. Convertir le Projet en Projet Maven

- **Créer un fichier `pom.xml`** :
  - Utilisez l'archétype Maven pour créer un nouveau projet ou ajoutez un `pom.xml` au projet existant.
- **Définir les dépendances** :
  - Ajoutez les dépendances nécessaires, par exemple :

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.18</version>
        </dependency>
    </dependencies>
    ```

- **Importer le projet Maven dans IntelliJ IDEA** :
  - Ouvrez le projet en tant que projet Maven pour bénéficier de la gestion automatique des dépendances.

#### 3. Introduction à Spring Boot

- **Documentation** : [Spring Boot - Guides](https://spring.io/guides)
- **Créer un nouveau projet Spring Boot** :
  - Utilisez [Spring Initializr](https://start.spring.io/) pour générer un projet avec les dépendances nécessaires (Spring Web, Spring Data JPA, PostgreSQL Driver).
- **Configurer l'application** :
  - Modifiez le fichier `application.properties` ou `application.yml` pour configurer la connexion à la base de données.
- **Recréer les entités** :
  - Utilisez les annotations JPA (`@Entity`, `@Id`, etc.) pour définir les entités correspondant aux tables de la base de données.
- **Créer les repositories** :
  - Créez des interfaces qui étendent `JpaRepository` pour chaque entité.
- **Développer les services et contrôleurs** :
  - Implémentez la logique métier dans des classes de service.
  - Exposez les fonctionnalités via des contrôleurs REST.

---

## Semaine 4 : Transformation en Application REST avec Spring Boot

### Objectifs :

- **API REST** :
  - Exposer les fonctionnalités de l'application via une API RESTful.
- **Contrôleurs REST** :
  - Créer des endpoints pour les opérations CRUD sur les tâches.

### Étapes :

#### 1. Créer les Contrôleurs REST

- **Annotations Spring** :
  - Utilisez `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, etc.
- **Endpoints** :
  - **GET /taches** : Récupérer toutes les tâches.
  - **GET /taches/{id}** : Récupérer une tâche par ID.
  - **POST /taches** : Créer une nouvelle tâche.
  - **PUT /taches/{id}** : Mettre à jour une tâche existante.
  - **DELETE /taches/{id}** : Supprimer une tâche.

#### 2. Tester l'API REST

- **Utiliser Postman ou Insomnia** :
  - Envoyez des requêtes HTTP pour tester les différents endpoints.
- **Gestion des erreurs** :
  - Implémentez une gestion des exceptions pour retourner des messages d'erreur appropriés.

---

## Semaine 5 : Formation en React

### Objectifs :

- **Apprendre React** :
  - Comprendre les bases de React pour le développement frontend.
- **Créer une Application React** :
  - Initialiser un projet avec Create React App.

### Étapes :

#### 1. Installation des Outils

- **Node.js et npm** :
  - [Télécharger Node.js](https://nodejs.org/en/download/)
- **Create React App** :
  - Installer globalement via npm :

    ```bash
    npm install -g create-react-app
    ```

#### 2. Créer le Projet React

- **Initialiser le projet** :

  ```bash
  create-react-app task-manager-frontend
