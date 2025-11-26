# TP4 : Création d'un cluster sur une machine

Le but de ce TP est de savoir créer un cluster et d'insérer des données avec filebeat.

Etapes :

- Installation d"un cluster
- Mise en place d'outils de supervision
- Simulations de pannes
- Injection de données avec filebeat
- Ajout de noeuds au cluster

## Installation

* De-zipper 3 fois Elasticsearch
* Configurer pour chaque noeud:
	- cluster name (le même pour tous)
	- node name (node1, node2, etc...)
	- HEAP SIZE : 2GB par noeud (config/jvm.options)
* Le premier noeud est démarré normalement : `bin/elasticsearch`
* On doit générer un token pour que les autres noeud puisse rejoindre (nouveau terminal) : 

```
bin/elasticsearch-create-enrollment-token -s node
```

Démarrer le noeud numéro 2 et 3:

```
bin/elasticsearch --enrollment-token "eyJ2ZXIi.........."
```

* Vérifier les traces de discovery dans les logs des noeuds

## Outils de monitoring

- Accéder à stack monitoring dans Kibana, relever les différentes métriques
- Installer https://elasticvue.com/ en extension firefox, ou récupérer la version desktop
- Connecter ElasticVue au cluster et ouvrir la vue "shards" pour comprendre la répartition des blocs de données.


## Simulation de panne

- Créer un index, 1 shard, 2 réplicas
- Vérifier que tous les shards sont assignés
- Couper le noeud 3
- Vérifier que la santé est yellow
- Remettre le noeud 3 dans le cluster
- Attendre que la santé repasse vert.

## Injection de données

Récupérer le fichier de données (access.log) dans votre répertoire utilisateur.

- Installer filebeat (téliécharger/dezipper)
- Activer le module apache (./filebeat module enable apache)
- Editer la configuration du module modules.d/apache.yml (chemin du fichier de données)
- Configurer la sortie de filebeat et la tester (./filebeat test output)
- Configurer la sortie kibana (filebeat.yml, secton setup.kibana)
- Générer les dashboards et pipelines (./filebeat setup dashboards)
- Lancer l'ingestion (./filebeat)

Accéder aux écrans de monitoring dans kibana, surveiller l'ingestion.

Exemple de configuration filebeat pour les outputs kibana/elasticsearch : 

```yml
setup.kibana:
  host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  preset: balanced
  protocol: "https"
  username: "elastic"
  password: "TyG_sWRTYP36YGsFUs=+"
  ssl.verification_mode: "none"
```



## Ajout d'un 4eme noeud elastic

- Ajouter un noeud elastic au cluster
- Vérfier qu'il a rejoint le cluster (`GET _cat/nodes`)
- Vérifier qu'il absorbe de la charge (`GET _cat/allocation`)


