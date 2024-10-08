# Middleware

### Objectif
L'objectif principal est de développer une infrastructure réseau supportant un jeu multi-joueur, en se focalisant sur la gestion des threads, la synchronisation, et l'utilisation de RMI (Remote Method Invocation) ainsi que la communication réseau via UDP.

### Outils Utilisés
- Java 1.17
- Maven

### Architecture du Projet

#### Lobby avec RMI :
- **Gestion du Lobby** : Utilise RMI pour orchestrer l'entrée des joueurs dans le jeu. Un joueur doit héberger une partie, et sera celui que les autres devront rejoindre.
- **Synchronisation des Joueurs** : Selon le nombre MAX de joueurs requis dans une partie (variable définie dans la classe `Lobby.java`), les joueurs devront attendre que le lobby soit plein pour que la partie démarre. Ils recevront ensuite la liste complète des joueurs afin de pouvoir se connecter de manière P2P en UDP entre eux. Pour cela nous utilisons un moniteur.

#### Communication via UDP :
- **Classe ServerUDP** : Gère l'envoi et la réception des paquets UDP.
- **Intégrité des Données** : Utilisation de CRC32 pour la vérification des données.
- **Thread Principal** : Gère le flux principal de l'application, notamment les actions du joueur local qui seront ensuite ajoutées dans une file d'attente pour le thread d'envoi.
- **Threads d'Envoi et de Réception UDP** : Gèrent la communication réseau. Un thread récupère les paquets dans la file d'envoi et les diffuse aux pairs, tandis que le thread de réception reçoit les paquets des autres joueurs et les traite en conséquence.
- **Synchronisation et Moniteurs** : Utilisés pour assurer la cohérence dans l'accès aux ressources partagées. Nous avons notamment utilisé des files d'attente pour l'envoi des données. Chaque action du joueur local engendre la création d'un `GamePacket` qui sera diffusé aux autres joueurs de la partie afin qu'ils maintiennent leur partie locale au même état.

### Problèmes et Limitations
- **Synchronisation Optimisée** : Manque d'amélioration sur la synchronisation des paquets UDP via le timestamp ou un système implémentant le temps causale, dû à des contraintes de temps liées à d'autres projets.
- **Problèmes de Sérialisation** : Certaines structures de données pourraient être plus abstraites ou redécoupées pour améliorer leur modularité et leur sérialisation.
- **Implémentation Incomplète** : Certaines fonctionnalités du jeu, comme la mort du joueur ou du boss, ne sont pas entièrement implémentées. L'accent a été mis sur le multithreading et la gestion des réseaux plutôt que sur les détails du gameplay. Nous ne voulions pas perdre de temps sur l'UI.

## Utilisation du Projet

### Importation

Pour éxécuter le projet avec maven:

```
cd BOSS_Middleware_project/
mvn exec:java
```

Ou via Intellij simplement, exécuter la classe `Main.java` situé: `src.main.java.com.middleware.app.Main`.

### Demo

Il faudrait idéalement 3 joueurs ou 2 en changeant la variable `MAX_PLAYERS` dans la classe `Lobby.java`. Pour cela en local il est possible de faire une copie du proje et de changer le port UDP de la classe main, d'un des deux projets. Il suffira d'utiliser l'adresse IP locale: `127.0.0.1` pour se connecter aux autres joueurs.

Changer le port UDP si besoin. Dans `src.main.java.com.middleware.app.Main`
```java
public static final int UDP_PORT = 6006;
public static final int LOBBY_PORT = 5006;
```


