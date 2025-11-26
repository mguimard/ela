# TP4 : Pipelines

Le but de ce TP est de savoir écrire une pipeline d'ingestion complexe pour maitriser l'ingestion de fichiers.

## Analyse des données

Nous avons à notre disposition un échantillon des données pour ce TP.

Voici l'échantillon brut :

```
2017-03-21T12:10:00+01:00;0087784009;Perpignan;76.0;11.0
2017-03-21T12:34:00+01:00;0087582825;Saint-Macaire;21.0;0.0
2017-03-21T14:45:00+01:00;0087582700;Beautiran;0.0;10.0
2017-03-21T14:47:00+01:00;0087393702;Massy TGV;;
;0087757526;Saint-Raphaël Valescure;;
```

Explication de la première ligne: 

- Le 21/03/2017 à 12h10, un agent SNCF a controllé la gare de Perpignan (gare numéro 0087784009), il a réalisé 76 points de controle, il a relevé 11 non conformités.

## Ecriture de la pipeline

Ces documents seront envoyés par un tiers sous cette forme :

```
POST sncf/_doc?pipeline=sncf-pipeline
{
    "message" : "2017-03-16T14:56:00+01:00;0087271007;Paris Gare du Nord;57.0;5.0"
}
```

A l'aide de l'API de simulation de pipeline, écrire les fonctions suivantes:

- Parsing CSV, découpage avec le point virgule
- Calcul du taux de conformité (ajout d'un champ supplémentaire)
  - `100 * (1 - problemes/observations)`

Exemple de simulation de pipeline :

```
POST _ingest/pipeline/_simulate
{
  "docs": [
    {"_source" : { "message" : "2017-03-21T12:10:00+01:00;0087784009;Perpignan;76.0;11.0" }},
    {"_source" : { "message" : "2017-03-21T12:34:00+01:00;0087582825;Saint-Macaire;21.0;0.0" }}
  ],
  "pipeline": {
    "processors": [
      {
        "set": {
          "field": "tag",
          "value": "hello world"
        }      
      },
      {
        "uppercase": {
          "field": "message"
        }
      }
    ]
  }
}
```

Le document devra avoir une forme similaire à cet exemple :

```
{
    "@timestamp" : "2017-03-16T10:56:00+01:00",
    "Gare" : "Morcenx",
    "CodeGare" : "0087673103",
    "Observations" : 47,
    "Problemes" : 12,
    "TauxConformite" : 74.4
}
```


Enregistrer la pipeline dans le système une fois qu'elle est bien testée.

## Préparation du mapping

Préparer le mapping final pour l'index qui accueillera les données.

```
PUT sncf
{
    "mappings" : {
        "properties" : {
            // ...
        }
    }
}
```

Ensuite nous ajouterons ces 2 champs virtuels dans le mapping (heure du jour, jour de la semaine):

```
PUT sncf/_mapping
{
  "runtime": {
    "hour_of_day": {
      "type": "double",
      "script": {
        "source": """
        if(doc['@timestamp'].size()!=0 )
          emit(doc['@timestamp'].value.getHour());
        else
          emit(0);
        """
      }
    },
    "day_of_week": {
      "type": "keyword",
      "script": {
        "source": """
        if(doc['@timestamp'].size() == 0)
          emit('NA');
        else
        emit(doc['@timestamp'].value.dayOfWeekEnum.getDisplayName(TextStyle.FULL, Locale.ROOT));
        """
      }
    }
  }
}
```

## Insertion des données avec Logstash

Télécharger logtash et extraire l'archive. 

https://www.elastic.co/cn/downloads/logstash

ATTENTION : ne pas extraire l'archive dans un chemin contenant des caractères spéciaux.

Extraire dans `/home/user` directement.

## Préparation de la donnée et de la configuration

Récupérer le fichier de données et le placer dans votre dossier utilisateur.

Créer un fichier nommé `sncf.conf` dans votre répertoire utilisateur.

```
input {
    file {
        start_position => "beginning"
        path => "CHEMIN_DU_FICHIER_CSV"
        sincedb_path => "/dev/null"
    }
}

output {
   elasticsearch {
        action => "index"
        index => "NOM_DE_L_INDEX_ICI"
        user => "elastic"
        password => "MDP_USER_ELASTIC"
        hosts => "localhost"
        ssl => true
        ssl_certificate_verification => false
        pipeline => "NOM_DE_LA_PIPELINE_ELASTIC"
    }
}

```


## Lancement de l'ingestion et validation des données

Lancer logstash avec la configuration en paramètre : 

```
bin/logstash -f /home/user/sncf.conf
```

Vérifier les données depuis kibana

```
GET /sncf/_count
GET /sncf/_search
```

## Utilisation de discover

Dans Kibana, accéder à "Discover", et créer la Data View pour notre index sncf.

Positionner le filtre temporel de discover sur les 15 dernières années. Utiliser ensuite la souris pour sélectionner la plage de temps plus finement.

Sauvegarder un tableau pour lister les interventions.

## Création d'un dashboard

Créer un nouveau dashboard vierge.

Y placer le tableau sauvegardé précédement.

Ajouter au dashboard les visualisations suivantes :

- Métrique: nombre de gares distinctes
- Métrique: nombre total d'observations
- Métrique: nombre total de problèmes
- Métrique: moyenne du taux de conformité
- Vertical bars: moyenne du nombre de controles par jour de la semaine
- Vertical bars: moyenne des problèmes par jour de la semaine
- Nuage de tag: nom de la gare (50 gares affichées)
- Camembert: Les gares les plus controlées
- Camembert: Les gares les plus conformes
- Camembert: Les gares les moins conforme
- TSVB: total des interventions / date

Ajouter des controles au dashboard (nom de la gare)





