# Authentification : Symfony

## Marche à suivre

### Télécharger Mailhog
- outil de test de mail. Permet de tester les capacités d'envoi et de réception de mail de l'application web développée.
- https://github.com/mailhog/MailHog/releases/tag/v1.0.1
- lancer le fichier .exe
- aller avec le navigateur sur **localhost:8025** pour voir afficher la page Mailhog

### Relier le système de mail à l'application
- dans le fichier **.env** : 
MAILER_DSN=smtp://localhost:1025 - paramétrer l'envoi de mail
- dans le fichier **config/packages/messenger.yaml** :
décommenter la ligne "sync" ligne 16
modifier "async" en "sync" ligne 24

### Créer une entité User sur Symfony
- symfony console make:user
- permet de créer une entité User spéciale car contient toutes les spécificités de sécurité. (propriété unique: au choix, email par ex + hashage de MDP)

### Paramétrer la base de données
- dans le fichier .env : DATABASE_URL="mysql://root@127.0.0.1:3306/SfUser"

### Créer la base de données
- dans le cmd : symfony console doctrine:database:create


### Créer le SecurityController + processus authentification : login
- dans le cmd :
- symfony console make:security:form-login pour créer le SecurityController
- choisir de générer une /logout url
- le formulaire de connexion est créé dans templates/security/login.html.twig
- le fichier config/packages/security.yaml est mis à jour

### Processus d'enregistrement : register form
- cans le cmd :
symfony console make:registration-form
- on valide pour ajouter la propriété #[UniqueEntity] à la classe User pour éviter la création de doublons
- on valide pour envoyer le mail de vérification de l'utilisateur après enregistrement (à installer le bundle :composer require symfonycasts/verify-email-bundle)
- on inclut pas de base l'user id dans le lien de vérification (paramètre de base)
- on définit l'email adresse qui va être utilisée pour le mail de vérification: admin@exemple.com
- on associe un nom : Admin Site
- on valide pour l'authentification automatique de l'utilisateur après enregistrement

### Migration de la base de données:
- symfony.exe console make:migration
- symfony.exe console doctrine:migrations:migrate

### Modifier le formulaire de register
- pour ajouter la vérification du mdp
- repeatedType Field : permet de répéter un champ
- 'first_options' => ['label' => 'Password'], 
- 'second_options' => ['label' => 'Repeat Password'], 
- faire les modifications nécessaires dans la vue register.html.twig : ajouter les champs etc

### Modifier les redirections : config/packages/security.yaml
- form_login :
    -- default_target_path : app_home 
    on indique la route vers laquelle on souhaite être redirigé après le success login (ici par exemple vers la route qui gère la page d'accueil)
- logout :
    target : app_login
    on indique qu'on souhaite être redirigé vers le formulaire de login quand on se déconnecte

### Mise en place de rôles admin/user
- décommenter les lignes 39 et 40 dans le security.yaml
- la méthode is_granted() de twig permet d'affecter des droits à certains rôles 