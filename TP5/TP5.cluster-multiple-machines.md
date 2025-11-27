# TP5 : Création cluster avec plusieurs machines

Le but de ce TP est de savoir créer et configurer un cluster avec plusieurs machines

## Pré-requis

* Former des groupes de 3 ou 4 participants

## Travail préalable

* Dessiner l'infra haute disponibilité du cluster à mettre en oeuvre
* Bien noter les IP, les noms de nodes, le nom du cluster, etc...
* Répartir les noeuds hot, warm, cold
* Répartir les noeuds dans des "racks"

## Mise en oeuvre des noeuds

* Effectuer la configuration des noeuds et les démarrer
* Vérifier qu'ils sont connectés (traces de discovery ou bien `GET _cat/nodes`)

## Préparation des règles ILM

- Modifier la règle ILM de filebeat pour accéler les rotations :
  - phase hot : rotation tous les 50mo ou bien toutes les 2 minutes
  - passage en phase warm au bout de 8 minutes
  - passage en phase cold au bout de 16 minutes
  - suppression des données au bout de 32 minutes

Il faudra aussi adapter le job qui vérifie l'état des index, pour que les rotations se fassent rapidement (toutes les 10 minutes par défaut)

```
PUT _cluster/settings
{
  "transient": {
    "indices.lifecycle.poll_interval": "5s"
  }
}
```

## Ingestion de données et monitoring

Un des participants va lancer filebeat, configré en load balancing sur les noeuds HOT

* Utiliser Kibana pour relever les métriques d'indexation
* Utiliser ElasticVue pour monitorer la répartition de la charge.

## Simulation de panne de plusieurs nodes

* Couper quelques noeuds du cluster.
* Vérfier que la santé du cluster reste bien en vert.
* Si la santé passe jaune, ou rouge, analyser la situation via les différentes API (notamment `GET _cat/shards`) 

