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

## Étape 1 : Installer Docker et IntelliJ IDEA

### Installation des Outils

- **Docker** : [Télécharger Docker Desktop](https://www.docker.com/products/docker-desktop)
  - Suivez les instructions d'installation pour votre système d'exploitation.
- **IntelliJ IDEA** : [Télécharger IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/)
  - Choisissez la version Community Edition, qui est gratuite.

---

## Configuration de la Base de Données PostgreSQL avec Docker

### 1. Télécharger le fichier `docker-compose.yaml`

Créez un fichier nommé `docker-compose.yaml` dans le répertoire de votre projet Java et copiez-y le contenu suivant :

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

### 2. Lancer la Base de Données

Dans un terminal, naviguez jusqu'au dossier contenant le fichier `docker-compose.yaml` et exécutez la commande suivante :

```bash
docker compose up
```

Cette commande télécharge l'image PostgreSQL et lance un conteneur avec la base de données configurée.

---

## Connexion à la Base de Données depuis IntelliJ IDEA

### 1. Ouvrir IntelliJ IDEA

### 2. Ajouter une Source de Données

- Allez dans **View > Tool Windows > Database**.
- Cliquez sur le bouton **+** et sélectionnez **Data Source > PostgreSQL**.

### 3. Configurer la Connexion

- **Host** : `localhost`
- **Port** : `5432`
- **Database** : `task`
- **User** : `user`
- **Password** : `password`

### 4. Télécharger le Pilote JDBC si Demandé

IntelliJ IDEA proposera de télécharger le pilote JDBC PostgreSQL. Cliquez sur **Download Driver**.

---

## Exécution du Script SQL pour Créer les Tables

### 1. Ouvrir la Console SQL

- Dans l'onglet **Database**, faites un clic droit sur la connexion `task` et sélectionnez **New > Query Console**.

### 2. Exécuter le Script SQL

Copiez et collez le script SQL suivant dans la console :

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

Exécutez le script en cliquant sur le bouton **Run** (icône en forme de triangle vert) ou en appuyant sur **Ctrl+Enter**.

---

## Téléchargement du Pilote JDBC PostgreSQL pour Java 8

Téléchargez le pilote JDBC PostgreSQL compatible avec Java 8 ici :

- [Télécharger JDBC PostgreSQL pour Java 8](https://jdbc.postgresql.org/download/)

Choisissez la version appropriée, par exemple **postgresql-42.2.18.jre7.jar**.

---

## Ajouter le Pilote JDBC à Votre Projet IntelliJ IDEA

### 1. Ajouter la Bibliothèque au Projet

- Faites un clic droit sur votre projet dans IntelliJ IDEA et sélectionnez **Open Module Settings** (ou appuyez sur `F4`).
- Dans l'onglet **Libraries**, cliquez sur le bouton **+** et sélectionnez **Java**.
- Naviguez jusqu'au fichier JAR du pilote JDBC que vous avez téléchargé et sélectionnez-le.
- Assurez-vous que la bibliothèque est associée à votre module (cochez la case correspondante).

---

## Développement de l'Application Console Java

### 1. Créer une Application Console Java pour Créer, Modifier et Ajouter des Tâches

- Créez un nouveau projet Java dans IntelliJ IDEA.
- Configurez le SDK Java 8 ou supérieur.
- Créez un package, par exemple `com.taskmanager`.
- Créez une classe principale `Main.java` dans ce package.

### 2. Établir la Connexion à la Base de Données

Dans votre classe `Main.java`, utilisez le pilote JDBC pour vous connecter à la base de données PostgreSQL.

Exemple de code :

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
            // Votre code ici pour interagir avec la base de données
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

### 3. Implémenter les Fonctionnalités CRUD

- **Créer** une nouvelle tâche :
  - Utilisez `PreparedStatement` pour insérer une nouvelle tâche dans la table `Taches`.
- **Modifier** une tâche existante :
  - Mettez à jour une tâche en utilisant son `id`.
- **Supprimer** une tâche :
  - Supprimez une tâche en fonction de son `id`.
- **Lister** toutes les tâches :
  - Utilisez une requête `SELECT` pour récupérer et afficher toutes les tâches.

### 4. Gérer les Entrées Utilisateur

- Utilisez la classe `Scanner` pour lire les entrées depuis la console.
- Proposez un menu interactif pour naviguer entre les options (Créer, Modifier, Supprimer, Lister les tâches).

---

## Gestion de Version avec Git

### 1. Formation sur Git

- **Documentation Officielle** : [Pro Git Book (français)](https://git-scm.com/book/fr/v2)
- **Tutoriels** :
  - [Apprendre Git en 15 minutes](https://try.github.io/levels/1/challenges/1)
  - [Guide de GitHub](https://guides.github.com/introduction/git-handbook/)

### 2. Initialiser le Dépôt Git

Dans le terminal, naviguez jusqu'au répertoire de votre projet Java et exécutez :

```bash
git init
```

### 3. Créer un Fichier `.gitignore`

Créez un fichier nommé `.gitignore` à la racine de votre projet avec le contenu suivant :

```
/out/
*.iml
.idea/
```

### 4. Faire le Premier Commit

```bash
git add .
git commit -m "Initial commit"
```

### 5. Configurer le Dépôt Distant sur GitHub

- Créez un nouveau dépôt sur GitHub.
- Associez le dépôt local au dépôt distant :

```bash
git remote add origin https://github.com/votre-utilisateur/votre-depot.git
git push -u origin master
```

---

## Répartition du Temps pour les Semaines Suivantes

### Semaines 2-3 : Introduction à Maven et Formation Approfondie en Spring Boot

#### Objectifs :

- **Maven** :
  - Comprendre le rôle de Maven dans la gestion des dépendances et la construction du projet.
  - Convertir le projet existant en un projet Maven.
- **Spring Boot** :
  - Découvrir le framework Spring Boot pour le développement d'applications web.
  - Recréer l'application en utilisant Spring Boot pour faciliter le développement et la gestion des dépendances.

#### Étapes :

1. **Introduction à Maven**
   - **Documentation** : [Apache Maven - Getting Started](https://maven.apache.org/guides/getting-started/index.html)
   - **Installer Maven** :
     - [Télécharger Maven](https://maven.apache.org/download.cgi)
     - Configurer les variables d'environnement (`MAVEN_HOME` et `PATH`).

2. **Convertir le Projet en Projet Maven**
   - Créer un fichier `pom.xml`.
   - Définir les dépendances nécessaires.
   - Importer le projet Maven dans IntelliJ IDEA.

3. **Introduction à Spring Boot**
   - **Documentation** : [Spring Boot - Guides](https://spring.io/guides)
   - Créer un nouveau projet Spring Boot via [Spring Initializr](https://start.spring.io/).
   - Configurer l'application (`application.properties` ou `application.yml`).
   - Recréer les entités avec les annotations JPA.
   - Créer les repositories, services et contrôleurs.

### Semaine 4 : Transformation en Application REST avec Spring Boot

#### Objectifs :

- **API REST** :
  - Exposer les fonctionnalités de l'application via une API REST.
- **Contrôleurs REST** :
  - Créer des endpoints pour les opérations CRUD sur les tâches.

#### Étapes :

1. **Créer les Contrôleurs REST**
   - Utiliser les annotations `@RestController`, `@RequestMapping`, etc.
   - Implémenter les endpoints :
     - `GET /taches`
     - `GET /taches/{id}`
     - `POST /taches`
     - `PUT /taches/{id}`
     - `DELETE /taches/{id}`

2. **Tester l'API REST**
   - Utiliser Postman ou Insomnia pour tester les endpoints.
   - Gérer les erreurs et exceptions.

### Semaine 5 : Formation en React

#### Objectifs :

- **Apprendre React** :
  - Comprendre les bases de React pour le développement frontend.
- **Créer une Application React** :
  - Initialiser un projet avec Create React App.

#### Étapes :

1. **Installation des Outils**
   - **Node.js et npm** : [Télécharger Node.js](https://nodejs.org/en/download/)
   - Installer Create React App :

     ```bash
     npm install -g create-react-app
     ```

2. **Créer le Projet React**

   ```bash
   create-react-app task-manager-frontend
   cd task-manager-frontend
   npm start
   ```

3. **Apprendre les Bases de React**
   - Composants, JSX, état, props, etc.

### Semaine 6 : Développement du Frontend et Intégration avec le Backend

#### Objectifs :

- **Développer le Frontend** :
  - Créer des composants pour lister, ajouter, modifier et supprimer des tâches.
- **Intégration avec l'API REST** :
  - Utiliser `fetch` ou `axios` pour communiquer avec le backend.

#### Étapes :

1. **Créer les Composants Principaux**
   - Liste des tâches.
   - Formulaire de tâche.
   - Navigation.

2. **Appels API**
   - Installer `axios` :

     ```bash
     npm install axios
     ```

   - Effectuer des requêtes HTTP pour interagir avec l'API.

3. **Tests et Débogage**
   - Vérifier le fonctionnement de l'application complète.

---

## Ressources Supplémentaires

- **Git** :
  - [Pro Git Book (français)](https://git-scm.com/book/fr/v2)
- **Maven** :
  - [Introduction à Maven](https://maven.apache.org/guides/getting-started/index.html)
- **Spring Boot** :
  - [Guides Spring](https://spring.io/guides)
- **React** :
  - [Documentation Officielle de React](https://fr.reactjs.org/)
- **Docker** :
  - [Documentation Docker](https://docs.docker.com/get-started/)
- **PostgreSQL JDBC Driver** :
  - [Télécharger le pilote JDBC PostgreSQL](https://jdbc.postgresql.org/download/)
- **IntelliJ IDEA** :
  - [Guide de démarrage IntelliJ IDEA](https://www.jetbrains.com/help/idea/discover-intellij-idea.html)

---

## Conclusion

Cette première étape vous permettra de vous familiariser avec l'environnement Java, la gestion de base de données avec PostgreSQL, et les outils essentiels comme Docker, IntelliJ IDEA et Git. Les étapes suivantes vous guideront à travers la transformation de cette application console en une application web complète avec un frontend en React.

---

N'hésitez pas à poser des questions ou à demander de l'aide si vous rencontrez des difficultés tout au long de cette formation. Bonne programmation !
