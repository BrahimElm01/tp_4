# TP 4 — Hibernate / JPA + MySQL

Mini-projet de persistance avec Hibernate 5 / JPA et MySQL 8 autour de deux entités liées : Salle et Machine. Ce guide couvre l’environnement, la config Hibernate et la façon d’exécuter le projet et les tests.

## 1) Préparer l’environnement

JDK : 8 ou plus récent (validé avec JDK 17)

Maven : 3.6+

MySQL : v8 en local (port 3306) avec le service démarré

Un utilisateur MySQL ayant des droits sur la base ciblée

Créer la base et un compte applicatif (conseillé) :

CREATE DATABASE base CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE USER IF NOT EXISTS 'app_user'@'localhost' IDENTIFIED BY 'change_me!';
GRANT ALL PRIVILEGES ON base.* TO 'app_user'@'localhost';
FLUSH PRIVILEGES;


Éviter d’utiliser root dans les applis ; préférez un utilisateur dédié.

## 2) Configurer Hibernate (MySQL 8)

Fichier : src/main/resources/hibernate.cfg.xml

<?xml version="1.0" encoding="UTF-8"?>
<hibernate-configuration>
  <session-factory>
    <!-- MySQL 8 -->
    <property name="hibernate.dialect">org.hibernate.dialect.MySQL8Dialect</property>
    <property name="hibernate.connection.driver_class">com.mysql.cj.jdbc.Driver</property>

    <!-- Connexion locale -->
    <property name="hibernate.connection.url">
      jdbc:mysql://localhost:3306/base?serverTimezone=UTC&amp;useSSL=false&amp;zeroDateTimeBehavior=CONVERT_TO_NULL
    </property>
    <property name="hibernate.connection.username">app_user</property>
    <property name="hibernate.connection.password">change_me!</property>

    <!-- Confort de dev -->
    <property name="hibernate.show_sql">true</property>
    <property name="hibernate.format_sql">true</property>
    <property name="hibernate.hbm2ddl.auto">update</property>

    <!-- Mapping des entités -->
    <mapping class="entities.Salle"/>
    <mapping class="entities.Machine"/>
  </session-factory>
</hibernate-configuration>


Notes rapides :

Utiliser com.mysql.cj.jdbc.Driver et MySQL8Dialect pour MySQL 8.

Les paramètres serverTimezone et useSSL évitent des avertissements.

Ne versionnez jamais de mots de passe réels : mettez un placeholder et configurez-le localement.

## 3) Modèle de données

Relation Salle (1) ↔ Machine (N). Le côté propriétaire est Machine via @ManyToOne.

Sur Machine, dateAchat est mappée avec @Temporal(TemporalType.DATE) (utilisation de java.util.Date côté tests).

Des helpers côté Salle peuvent être ajoutés pour gérer la relation bidirectionnelle si besoin.

## 4) Lancer l’exemple et les tests

Exécuter la démo (insertion + affichage) : lancer src/main/java/test/Test.java depuis l’IDE.

Lancer les tests Maven :

mvn -q -DskipTests=false test


Couverture des tests :

SalleServiceTest : opérations CRUD + findAll

MachineServiceTest : CRUD + requête nommée findBetweenDate

Les tests préparent et nettoient leurs données ; la base peut être vide au départ.

## 5) Gestion des ressources

La SessionFactory est créée au démarrage. Pour une application standalone, fermer proprement en fin d’exécution : appeler HibernateUtil.shutdown().

## 6) Résultats attendus (captures à insérer)

Exécution IDE : insertion de quelques salles/machines et affichage en console

Tests JUnit au vert (services SalleService et MachineService)

Vue MySQL (phpMyAdmin) : schéma base, tables salles et machines

Astuces

En dev, hibernate.hbm2ddl.auto=update accélère la mise en place ; en prod, migrer via des scripts (Flyway/Liquibase).

Si vous voyez “Cannot resolve table” dans l’IDE, synchronisez la connexion de l’onglet Database ou exécutez l’app pour que Hibernate crée le schéma.

## 7)résultats
<img width="1905" height="971" alt="hibernate" src="https://github.com/user-attachments/assets/6b993da1-ceaa-488f-8b3c-aee0933a3c16" />
<img width="1915" height="1012" alt="MachineServicetest" src="https://github.com/user-attachments/assets/c246b613-aba2-494a-8e4e-52f4b3ff3dae" />
<img width="1907" height="1016" alt="SalleServicetest" src="https://github.com/user-attachments/assets/c151a180-c08c-4e4f-9ca8-b6478f5f068a" />
<img width="1630" height="1030" alt="bd" src="https://github.com/user-attachments/assets/ed4a8b78-35be-448c-8777-d3aac84e914f" />
<img width="1620" height="1025" alt="bd1" src="https://github.com/user-attachments/assets/39197a9e-72ca-4ec2-abb8-a39677035aff" />
<img width="1615" height="1026" alt="bd2" src="https://github.com/user-attachments/assets/ece2edee-aee4-4596-a283-9ff5387697aa" />
