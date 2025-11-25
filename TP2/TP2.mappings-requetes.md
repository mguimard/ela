# TP2 : Optimisation du mapping et introduction aux recherches

Le but de ce TP est d'apprendre à 

* Stocker intelligement des données dans elasticsearch
* Effectuer des requêtes de recherche simples

---

## 1ère partie

### Analyse des mappings de l'index bank

Utiliser l'API _mappings pour afficher les mappings :

```
GET bank/_mappings
```

A l'aide de la documentation officielle, créer un mapping optimisé.

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html

```
PUT bank2
{
    "mappings" : {
        "properties": {
            "nom_du_champ": {
                "type" : "type_du_champ"
            },
            ...
        }
    }
}
```

Ré-insérer les données avec la commande curl du TP précédent (penser à modifier le nom de l'index dans l'URL par bank2)

Comparer la taille prise sur le disque.

```
GET _cat/indices?v
```

## 2ème partie

### Découverte des queries

** Les différents types de query**

Utilisation de la documentation et parcours des méthodes de recherche existantes

https://www.elastic.co/guide/en/elasticsearch/reference/current/term-level-queries.html

### Recherches "match" "term" et "range"

Rechercher des documents avec les requêtes "match" ou "term" et "range"

* Comptes ayant "street" dans le champ adresse (match ou term)
* Comptes ayant plus de 25 ans (range)
* Comptes ayant entre 25000 dollars et 35000 dollars (range)

### Requêtes "fuzzy" "wildcard" et "prefix"

Avec l'aide de la documentation

https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-fuzzy-query.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-wildcard-query.html

Ecrire les requêtes de recherche suivantes

* Compte dont le prénom est "Rose" à quelques fautes près (fuzzy)
* Compte dont le prénom commence par "R" (prefix)
* Compte dont le prénom contient un "E" (wildcard)

Avec la documentation des "bool queries"

* Rechercher les comptes dont l'age est inférieur à 25 ans et le compte en banque supérieur à 25000.

Bonus : booster le score des comptes dont le genre est féminin.

Bonus 2 : Répéter la 2eme partie sur les données de l'index bank et conclure sur l'utilité des mappings (index créé dans le TP1).
