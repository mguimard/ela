# TP1 : Installation type utilisant un serveur Elasticsearch pour de l'indexation de données.

Le but de ce TP est d'apprendre à 

* Installer elasticsearch
* Installer Kibana
* Découvrir les commandes de base de la console DevTools
* Injecter un fichier de données
* Découvrir les fonctions de base de Kibana

---

Prérequis de l'environnement :

* CURL (inclus dans git bash, ou à télécharger et installer)
* Terminal (git bash, cmder ou autre)
* Navigateur Web (Edge, Firefox, Google Chrome ...)

---
## 1ere partie

### Installation de Elasticsearch et Kibana

#### Installation d'Elasticsearch

* Récupérer l'archive sur la page https://www.elastic.co/fr/downloads/elasticsearch
* Décompresser l'archive
* Ouvrir un terminal dans le dossier décompressé, puis lancer la commande suivante

    bin/elasticsearch

Bien noter les mots de passes affichés au prémier démarrage.

Vérifier que le service est bien lancé en accédant à https://localhost:9200 depuis votre navigateur.

#### Installation de Kibana

* Récupérer l'archive sur la page https://www.elastic.co/fr/downloads/kibana
* Décompresser l'archive
* Ouvrir un terminal dans le dossier décompressé, puis lancer la commande suivante

    bin/kibana

Vérifier que le service est bien lancé en accédant à http://localhost:5601 depuis votre navigateur.

### Utilisation de la console "dev tools" dans Kibana

* Accèder à Kibana http://localhost:5601
* Naviguer dans le menu "Dev tools" pour accéder à la console.

Découvrir l'auto-complétion, les options et outils de la console, et exécuter les requêtes suivantes : 

```
GET /
GET /_cluster/health
GET /_nodes
GET /_stats
GET /_cat/indices?v
```

Insérer un peu de données, exemple :

```
POST /employes/_doc
{
	"name":"Joe",
	"age": 32
}
```

Demander à nouveau les informations de santé du cluster et des indices


```
GET /_cluster/health
GET /_cat/indices?v
```

Pourquoi est-il en statut yellow ?

Demander la liste des shards pour analyser la répartition

```
GET /_cat/shards?v
```

### 2ème partie : insertion d'un jeu de données

### Injection de donnés

Insertion de 1000 enregistrements avec curl et la bulk API

* Ouvrir un terminal
* Lancer la commande suivante, dans le dossier qui contient le jeu de données `accounts.bulk`

`curl -XPOST https://localhost:9200/bank/_bulk -H"Content-Type: application/json" --data-binary @accounts.bulk --insecure -u elastic`


Le fichier accounts.bulk contient une liste comptes bancaires, dont voici un élément :

```
{
	"account_number": 13,
	"balance": 32838,
	"firstname": "Nanette",
	"lastname": "Bates",
	"age": 28,
	"gender": "F",
	"address": "789 Madison Street",
	"employer": "Quility",
	"email": "nanettebates@quility.com",
	"city": "Nogal",
	"state": "VA"
}
```


### Navigation et découverte des données avec Kibana

Premiers pas avec Kibana

* Naviguer dans le menu Stack Management -> Index patterns
* Ajouter le nom de l'index créé (bank) dans un nouvel "index pattern"
* Utiliser ensuite l'onglet discover pour visualiser les données
* Faire des recherches simples dans le champ principal (exemple : name:Joe,age:28)

### 3ème partie : création d'un dashboard

### Création de tables

* Naviguer dans le menu discover
* Créer un tableau (cliquer sur le nom des champs à visualiser)
* Afficher l'age, le prénom, nom de famille, compte en banque, etc...)
* Sauvegarder des vues, triées selon l'age, le compte en banque, etc...
* Bien nommer ces vues et les préfixer, exemple : `[BANK] Les plus riches`

### Création de graphiques

* Naviguer dans le menu "Visualize"
* Cliquer sur "Create visualization", puis "aggregation based", puis "metric"

Afficher et sauvegarder les métriques suivantes :

* Moyenne d'age
* Nombre de comptes
* Max, min et moyenne du compte en banque

### Création d'un dashboard

* Naviguer dans le menu dashboard
* Ajouter les tables et graphiques créés depuis le bouton "library"
* Mettre en forme les différents éléments
* Sauvegarder le dashboard, et repérer les fonctionnalités de partage.

### Bonus

Découvrir les "agrégations" avec les autres types de graphiques (area, pie chart, line chart, ...)

* Naviguer dans "Visualize" -> "Aggregation based" -> "Vertical bars"
* Dans la partie métric ajouter la moyenne de compte en banque
* Dans la partie bucket, découper le graphique sur l'axe X (type histogram sur le champ age, interval = 5 ans)
* Ajouter un second bucket (type terms sur le champ gender) pour découper les séries.
* Sauvegarder et ajouter au dashboard.
* Créer d'autres visualisations selon leur pertinence
