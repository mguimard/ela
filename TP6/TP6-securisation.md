# TP6 : Sécurisation d'Elasticsearch

Le but de ce TP est de savoir mettre en place une authentification basique d'Elasticsearch et un contrôle d'accès aux données.

Nous allons aussi mettre en place des espaces Kibana et restreindre les fonctionnalités.

## Création d'utilisateurs et de mots de passe

Stopper ElasticSeach si celui-ci est déjà démarré.

Modifier le fichier de configuration pour activer la sécurité. Ajouter à la fin du fichier elasticsearch.yml la ligne suivante :

`xpack.security.enabled: true`

Redémarrer le cluster.

Générer les mots de passes des utilisateurs réservés.

Depuis un nouveau terminal dans le dossier elasticsearch:

```
bin/elasticsearch-setup-passwords interactive
```

Entrer les différents mots de passes que vous choisirez.

## Configuration de Kibana

Stopper Kibana et éditer config/kibana.yml pour définir le user/mot de passe de kibana. Redémarrer kibana.

## Création de rôles depuis Kibana

A l'aide de la documentation https://www.elastic.co/guide/en/kibana/current/using-kibana-with-security.html, mettre en place la sécurité dans Kibana.

* Accéder au menu "Stack managment -> Roles" et créer un role qui donne l'accès en lecture à l'index bank
* Accéder au menu "Stack managment -> Roles" et créer un utilisateur et lui affecter le rôle précédement créé.
* Vérifier que l'utilisateur créé a bien accès en lecture à l'index bank.

## Création d'espaces kibana

Créer un esapce kibana nommé "bank" dans lequel vous associerez l'utilisateur précédement créé. Donner accès au menu "discover" seulement.

Tester la connexion à kibana avec cet utilisateur.

