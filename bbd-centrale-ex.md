# Explication de la base

- address : une adresse, si un "user_uuid" est renseigné, alors cette adresse est celle d'un utilisateur (en plus de potentiellement être celle d'une vente)
- brand : les marques de voiture
- favorite : les annonces mises en favoris par les utilisateurs
- fuel : les types de carburant
- image : les images des annonces
- listing : les annonces
- model : les modèles de voitures
- user : les utilisateurs

## 1/ Afficher les informations des utilisateurs et, s'ils en possèdent une, leur adresse

```sql
SELECT * FROM `user` LEFT JOIN address ON address.user_uuid = user.uuid;
```

## 2/ Afficher les 20 dernières annonces, on affichera seulement les colonnes suivantes : title, price et mileage. Price sera concaténé à "€" et mileage à "km" (PS : les prices sont en centimes, il faut les diviser par 100)

```sql
SELECT listing.title, listing.created_at, CONCAT(listing.price / 100, 2), '€', CONCAT(listing.mileage, 'km' ), listing.created_at AS price FROM `listing` ORDER BY created_at LIMIT 20;


SELECT listing.title,
        listing.created_at,
        CONCAT(ROUND(listing.price/100, 2), '€') AS 'price',
        CONCAT(listing.mileage, 'km') AS 'km'

FROM listing
ORDER BY listing.created_at DESC
LIMIT 20;
```

## 3/ Afficher le nombre d'annonces par marque de voiture

```sql
SELECT  brand.name AS 'marque',
        COUNT(*)

FROM brand

JOIN model ON model.brand_id = brand.id
JOIN listing ON listing.model_id = model.id

GROUP BY brand.name;
```

## 4/ Afficher les annonces de voitures de la marque "Renault" ayant au moins 50000km

```sql
SELECT listing.title,
       listing.price,
       listing.mileage,
       model.name AS 'model',
       brand.name AS 'brand'
FROM listing
JOIN model ON model.id = listing.model_id
JOIN brand ON brand.id = model.brand_id
WHERE brand.name = 'Renault' AND listing.mileage >= 50000;
```

## 5/ Afficher les annonces de voitures de la marque "Ford" ayant au moins 30000km et un prix maxmimum de 12000€ (Attention aux centimes...)

```sql
SELECT listing.title,
       CONCAT(ROUND(listing.price/100, 2), '€') AS 'price',
       listing.mileage,
       model.name AS 'model',
       brand.name AS 'brand'
FROM listing
JOIN model ON model.id = listing.model_id
JOIN brand ON brand.id = model.brand_id
WHERE brand.name = 'Ford' AND listing.mileage >= 30000 AND listing.price <= 1200000;
-- WHERE brand.name = 'Ford' AND listing.mileage >= 30000 AND listing.price <= 12000 * 100;
-- WHERE brand.name = 'Ford' AND listing.mileage >= 30000 AND (listing.price/100) <= 12000;
```

## 6/ Afficher les emails des utilisateurs ayant fait au moins une annonce dans les 6 derniers mois

```sql
SELECT DISTINCT user.email
FROM user
JOIN listing ON listing.owner_uuid = user.uuid
WHERE listing.created_at >= DATE_SUB(CURDATE(), INTERVAL 6 MONTH);
```

CURDATE() récupérer la date courante [MySQL]

DATE_SUB() soustraire une valeur au format TIME à une date [MySQL]

## 7/ Afficher le nombre d'annonces déposées par années (Exemple : "2025 → 10", "2024 → 59", etc...)

```sql
SELECT YEAR(listing.created_at) AS 'année',
       COUNT(*) AS 'nombre_annonces'
FROM listing
GROUP BY YEAR(listing.created_at)
ORDER BY YEAR(listing.created_at) DESC;
```

YEAR() extraire lannée dune date [MySQL, SQL Server]

## 8/ Afficher les 5 modèles de voitures les plus mis en vente

```sql
SELECT model.name AS 'modèle',
       COUNT(*) AS 'nombre_annonces'
FROM model
JOIN listing ON listing.model_id = model.id
GROUP BY model.name
ORDER BY COUNT(*) DESC
LIMIT 5;
```

## 9/ Afficher les annonces pour les voitures électriques

```sql
SELECT listing.title,
        CONCAT(ROUND(listing.price/100, 2), '€') AS 'price',
        listing.mileage,
        listing.fuel_id AS 'type',
        model.name AS 'model',
        brand.name AS 'brand'
FROM listing
JOIN model ON model.id = listing.model_id
JOIN brand ON brand.id = model.brand_id
JOIN fuel ON fuel.id = listing.fuel_id
WHERE fuel.type = 'Electric';
```

## 10/ Afficher les modèles de voiture n'ayant pas de modèle électrique en annonce

```sql
SELECT model.name AS 'modèle',
       brand.name AS 'marque'
FROM model
JOIN brand ON brand.id = model.brand_id
WHERE model.id NOT IN (
    SELECT DISTINCT listing.model_id
    FROM listing
    JOIN fuel ON fuel.id = listing.fuel_id
    WHERE fuel.type = 'Electric'
)
GROUP BY model.name, brand.name;

SELECT model.name AS 'modèle',
       brand.name AS 'marque',
       GROUP_CONCAT(DISTINCT fuel.type) AS 'types_de_carburant'
FROM model
JOIN brand ON brand.id = model.brand_id
JOIN listing ON listing.model_id = model.id
JOIN fuel ON fuel.id = listing.fuel_id
WHERE model.id NOT IN (
    SELECT listing.model_id
    FROM listing
    JOIN fuel ON fuel.id = listing.fuel_id
    WHERE fuel.type = 'Electric'
)
GROUP BY model.name, brand.name;
```

GROUP_CONCAT() permet de regrouper les valeurs non nulles dun groupe en une chaîne de caractère. Cela est utile pour regrouper des résultats en une seule ligne

DISTINCT est très pratique pour éviter les résultats en doubles. Cependant, pour optimiser les performances il est préférable dutiliser la commande SQL GROUP BY lorsque cest possible.

## 10/B Afficher les modèles de voiture n'ayant pas de modèle gazol en annonce

```sql
SELECT model.name, model.id
FROM model
WHERE model.id NOT IN (
    SELECT DISTINCT model_id
    FROM `listing`
    WHERE `fuel_id` = 2
);
```

## 11/ Afficher les 20 dernières annonces, mais de la page 3 (on considère que l'on a 20 annonces par page)

```sql
SELECT listing.title,
       CONCAT(ROUND(listing.price/100, 2), '€') AS 'price',
       listing.mileage,
       model.name AS 'model',
       brand.name AS 'brand'
FROM listing
JOIN model ON model.id = listing.model_id
JOIN brand ON brand.id = model.brand_id
ORDER BY listing.created_at DESC
LIMIT 20 OFFSET 40;

SELECT * FROM `listing`
ORDER BY listing.created_at DESC
LIMIT 20 OFFSET 40;
```

## 12/ Afficher le nombre d'adresses enregistrées par ville, les trier par ordre décroissant

```sql

SELECT COUNT(*), city FROM `address`
GROUP BY address.city
ORDER BY COUNT(*) DESC;
```

## 13/ Afficher les adresses, dont le "street_name" est présent plus dune fois, et trier par ordre décroissant

```sql

SELECT COUNT(*), address.street_name
FROM `address`
GROUP BY address.street_name
HAVING COUNT(*) > 1
ORDER BY COUNT(*) DESC;
```

## 14/ Afficher les user n'ayant pas défini d'adresses (leur "uuid" n'est pas présent dans la table "address" )

```sql
SELECT * FROM user
LEFT JOIN address ON address.user_uuid = user.uuid
WHERE address.user_uuid IS NUll;
```

## 15/ Afficher les annonces n'ayant aucune image (si il y en a ?)

```sql
SELECT * FROM `listing`
LEFT JOIN image ON image.listing_uuid = listing.uuid
WHERE image.listing_uuid IS NULL;
```

## 16/ Afficher les annonces ayant plusieurs images

```sql
SELECT listing.title,
COUNT(image.uuid) AS 'nombre_images'
FROM listing
JOIN image ON image.listing_uuid = listing.uuid
GROUP BY listing.uuid
HAVING COUNT(image.uuid) > 1;
```

## 17/ Afficher par marque, leur somme totale mise en vente, les trier par ordre décroissant

```sql
SELECT brand.name AS 'marque',
        SUM(listing.price) AS 'somme_totale'
        FROM brand
        JOIN model ON model.brand_id = brand.id
        JOIN listing ON listing.model_id = model.id
        GROUP BY brand.id
        ORDER BY SUM(listing.price) DESC;

```

## 18/ Afficher le nombre d'annonce déposée par mois, depuis le début de l'année 2025

```sql
SELECT MONTH(listing.created_at) AS 'mois',
       COUNT(*) AS 'nombre_annonces'
       FROM listing
       WHERE YEAR(listing.created_at) = 2025
       GROUP BY MONTH(listing.created_at)
       ORDER BY MONTH(listing.created_at);

```
