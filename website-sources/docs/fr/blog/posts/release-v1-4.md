---
date: 2026-07-26
categories:
  - Release
tags:
  - 1.4.0
---

# Sortie de RecoveryBox v1.4.0

La version 1.4.0 de RecoveryBox est sur le point d'être publiée. Cette version apporte de nouvelles fonctionnalités et des améliorations significatives.

J'ai pris le temps de revoir entièrement le fonctionnement du script d'installation, mais aussi la technologie utilisée pour déployer les services. Le projet est passé d'un déploiement en Bash pur à une approche hybride combinant Bash et Ansible. Ce changement s'accompagne d'un changement de philosophie dans la manière de déployer les services, dans le but de rendre RecoveryBox plus flexible et plus facile à maintenir.

La v1.4.0 marque également la sortie du site web sur lequel vous lisez ces lignes. Bien qu'encore un peu austère, il s'étoffera au fil du temps.


<!-- more -->

## Ansible {: .rb-section-title}

### Qu'est-ce qu'Ansible ? {: .rb-section-subtitle}

Ansible est un outil d'automatisation de déploiement et de configuration. Il permet de déployer des services sur des machines distantes de manière simple et efficace. Son fonctionnement par tâches permet de valider que chaque étape du déploiement s'est bien déroulée. En cas d'erreur, Ansible arrête le déploiement et affiche un message d'erreur explicite. C'est un outil que j'utilise quotidiennement dans mon travail, et il m'a semblé naturel de refondre complètement l'installateur pour l'utiliser.

Pour être honnête, le code de l'installateur était propre, mais il devenait de plus en plus complexe à maintenir. Chaque brique était gérée par une fonction, chaque fonction avait ses propres conditions d'exécution et ses dépendances, et le tout se trouvait dans un seul fichier. C'est une architecture qui fonctionne bien pour un petit script, mais `recovery_box_install.sh` avait atteint 925 lignes. Je passais mon temps à jouer de la molette ou à faire des `Ctrl+F` pour m'y retrouver.

### Intégration {: .rb-section-subtitle}

La philosophie que j'applique avec Ansible est assez simple. Il y a un playbook d'installation nommé `Install.yml`, qui appelle lui-même le rôle `recoverybox` et plus particulièrement sa tâche `main.yml`. Cette tâche appelle elle-même d'autres fichiers de tâches en fonction de conditions liées à des variables (nous y reviendrons). Chaque fichier de tâche contient donc une liste d'actions à effectuer, regroupées par sujet.

Pour illustrer, voici un extrait du fichier `main.yml` du rôle `recoverybox` :

??? note "main.yml"
    ```yaml
    - name: Install Access Point
      ansible.builtin.import_tasks: install_access_point.yml
      tags:
        - install_access_point
        - dry_run

    - name: Install Apache2
      ansible.builtin.import_tasks: install_apache2.yml
      tags:
        - install_apache2
        - dry_run
      when: recoverybox_enable_apache

    - name: Install rb-library
      ansible.builtin.import_tasks: install_rb_library.yml
      tags:
        - install_library
        - dry_run
      when:
        - recoverybox_enable_library
        - recoverybox_enable_apache
    ```

Et voici un extrait du fichier `install_apache2.yml` :

??? note "install_apache2.yml"
    ```yaml
    - name: Install Apache2
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Enable proxy modules
      ansible.builtin.command:
        cmd: "a2enmod {{ item }}"
      changed_when: false
      loop:
        - proxy
        - proxy_http
        - proxy_wstunnel
        - rewrite

    - name: Create /data/www directory
      ansible.builtin.file:
        path: /data/www
        state: directory
        owner: root
        group: root
        mode: '0755'
    ```

Comme expliqué précédemment, Ansible utilise des variables pour créer des conditions. Mais en réalité, il utilise des variables pour tout. Tout peut être variabilisé, ce qui permet d'avoir des actions dynamiques lors de l'exécution. On peut décider d'exécuter telle ou telle tâche, mais aussi remplir un fichier avec des valeurs définies en amont, voire définies à la volée suite à d'autres actions précédentes. Toutes les variables ont une valeur par défaut située dans le fichier `defaults/main.yml` du rôle `recoverybox`. Ces variables peuvent être surchargées par des fichiers. C'est ce qu'utilise le nouveau script `RecoveryBox_install.sh`.

Le fait de tout passer en variable permet aussi de gérer plus facilement les mises à jour. Chaque module (ou presque) a une variable précisant sa version. La gestion des mises à jour en est donc simplifiée. Techniquement, il suffira de modifier la valeur de la variable pour que le module soit mis à jour lors de la prochaine exécution du script d'installation. Il est aussi possible de faire des mises à jour partielles en ne modifiant que certaines variables. Cela permet de mettre à jour un module sans toucher aux autres.

Le fait de tout passer en variable permet aussi d'activer ou non un module en fonction de la valeur d'une variable. J'ai donc pu modifier `rbstatus` ainsi que la page d'accueil `http://recovery.box` pour que les services affichés soient dynamiques en fonction des modules activés. Si l'on désactive le module `kiwix`, il n'apparaîtra plus dans la page d'accueil ni dans `rbstatus` et ne sera plus accessible via l'interface web. Cela permet de personnaliser RecoveryBox en fonction de ses besoins.

### Et le script d'installation dans tout ça ? {: .rb-section-subtitle}

`RecoveryBox_install.sh`, en plus de lancer le playbook Ansible, va créer le fichier de variables `/etc/recoverybox/custom_config.yml` à partir du questionnaire proposé à l'utilisateur. Cela va surcharger une partie des variables par défaut du rôle `recoverybox`. Il est toujours possible de modifier ce fichier manuellement afin d'agir sur d'autres variables qui ne sont pas gérées par le script d'installation. Typiquement, si l'on veut modifier les canaux Wi-Fi ou le mot de passe du point d'accès, il faudra le faire manuellement dans ce fichier. On peut aussi s'en servir pour télécharger d'autres fichiers ZIM pour Kiwix, ou ne désactiver que le Wikipédia anglais ou ne souhaiter que les versions avec images. Bref, c'est un fichier de configuration qui permet de personnaliser RecoveryBox à souhait, et un administrateur système pourrait même se passer du script d'installation pour tout gérer via Ansible (la configuration réseau et le passage à systemd-networkd devront toutefois être faits en amont).

### Industrialisation {: .rb-section-subtitle}

L'intérêt de passer par Ansible est aussi la possibilité d'industrialiser le déploiement et les mises à jour de RecoveryBox. Il est possible de déployer RecoveryBox sur plusieurs machines en même temps via une machine tierce qui viendra exécuter les actions sur les machines cibles. La logique est assez simple : on prend une RecoveryBox ou un serveur qui servira de machine de déploiement, on y installe Ansible et on y configure un fichier d'inventaire avec les machines cibles. Cet inventaire, c'est un fichier qui peut être très simple, avec simplement pour chaque machine son IP, le nom d'utilisateur et le mot de passe. De cette manière, on pourra déployer RecoveryBox sur plusieurs machines en même temps et les mettre à jour en une seule commande. C'est un gain de temps considérable pour les administrateurs systèmes qui gèrent plusieurs RecoveryBox.

Mais on peut aussi pousser le vice encore plus loin avec l'inventaire. On peut personnaliser les configurations de chaque machine via celui-ci, en gros on rapatrie `custom_config.yml` dans l'inventaire. Donc on peut, avec une seule commande, déployer une machine qui ne fait que point d'accès et mise à disposition de documents, tout en déployant en même temps une autre machine avec Wikipédia en croate et la carte de Croatie dans Kiwix, et encore une autre machine dont le hotspot a été personnalisé pour fonctionner en Wi-Fi 6 avec un autre nom de SSID.

À l'heure actuelle, ça ne me sert strictement à rien, soyons honnêtes. J'ai voulu penser les choses de manière à pouvoir industrialiser le déploiement et la maintenance facilement, car j'avais en tête que ce projet pourrait intéresser des associations, des ONG ou même des services comme la sécurité civile, qui sont souvent déployés sur des événements nécessitant une centralisation des informations et des communications. Mais pour l'instant, je n'ai pas de retour d'expérience sur ce point. Si vous êtes intéressé par ce genre d'utilisation, n'hésitez pas à me contacter pour qu'on puisse en discuter.

## Le site web {: .rb-section-title}

Avec l'arrivée de la version 1.4.0, j'ai intégré le wiki dans le projet directement. Précédemment, j'utilisais le wiki de GitHub, mais je ne trouvais pas ça agréable à utiliser. On est bien trop limité dans la mise en page et le rendu n'est pas terrible. Le gros point bloquant à mes yeux était que le wiki, pour faire fonctionner cette solution hors ligne, se trouvait... en ligne. L'utilité de ce wiki était proche de zéro (en plus du fait qu'il n'était pas complet, la doc c'est toujours le plus chiant à faire). J'ai donc décidé de l'intégrer directement dans le projet et de le rendre accessible via l'interface web de la RecoveryBox.

Mais là se posait un nouveau problème. L'avantage d'avoir un wiki sur GitHub, c'est qu'il peut être consulté par tout le monde, avant de télécharger le projet. C'est difficile de suivre le quickstart du projet quand il faut aller lire des fichiers Markdown dans le repo. J'ai donc décidé d'acheter mon nom de domaine et de mettre en ligne le wiki. Mais un wiki tout seul sur un site web, c'est pas très sexy. J'ai donc décidé de faire un site web complet avec une page d'accueil, une page de blog et le wiki. Le site est encore en cours de développement mais il est déjà fonctionnel.

Le site web, tout comme le wiki, est développé avec MkDocs Material. C'est un générateur de site statique qui permet de créer des sites web à partir de fichiers Markdown. Je découvre l'outil au fur et à mesure de mes besoins. C'est assez pratique pour faire des sites web statiques sans trop de fioritures. Je voulais une solution facile à implémenter et à mettre à jour, et qui me permettrait de réintégrer facilement les fichiers Markdown du wiki originel. Le web, c'est pas trop mon fort, je suis clairement plus à l'aise avec du Bash, du Python ou le terminal d'un switch, du coup j'y vais à grand coup d'IA, surtout pour customiser le design. Je prends tout de même le temps de relire et d'essayer de comprendre ce que l'agent a fait, mais je ne saurais pas dire si c'est super optimisé. Mais ça marche et c'est tout ce que je demande.

Actuellement, le site web est hébergé chez OVH. Quand on prend un nom de domaine chez eux, ils offrent un "hébergement web" gratuit. On parle de 100 Mo de stockage, c'est pas non plus la panacée, mais c'est parfait pour un site vitrine ou un bout de wiki tant qu'il n'y a pas trop de photos. Pour l'instant, ça fait l'affaire. J'ajouterai sans doute des photos pour illustrer les articles ou des captures d'écran dans le wiki, mais on verra le moment venu. Étant donné que tout est stocké dans des dépôts Git, c'est ultra facile de redéployer le site au besoin. Et puis avec les GitHub Actions, le wiki se met à jour tout seul quand je pousse des modifications (il faudrait que j'en parle un jour).

En attendant, au moment où j'écris ces lignes, la 1.4.0 n'est pas encore sortie. Il me reste encore la partie d'activation des services à intégrer à la partie Ansible et surtout un déploiement complet à tester sur une machine vierge. Mais je voulais quand même vous parler de cette version car elle est assez importante dans l'évolution du projet. C'est une refonte profonde de ma méthode de travail et de la mécanique en arrière-plan. J'espère que vous apprécierez les changements et que vous trouverez le site web utile. N'hésitez pas à me faire des retours, des suggestions ou des critiques constructives, c'est toujours apprécié.