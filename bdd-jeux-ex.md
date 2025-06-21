# Explication de la base

- category : les categories de jeux, comme "FPS", "Action", etc
- country : les pays disponibles, les langues, drapeaux etc
- game : les jeux
- game_category : table relationnelle pour représenter les catégories dans lesquelles un jeu est disponible
- game_country : table relationnelle pour représenter les langues dans lesquelles un jeu est disponible
- publisher : les éditeurs de jeux
- review : les commentaire déposés par les utilisateurs sur un jeu
- user : les utilisateurs
- user_own_game : les jeux possédés par les utilisateurs

## 1/ Affichez les 9 derniers jeux sortis ("game.published_at")

```sql
SELECT *
FROM game
ORDER BY published_at DESC
LIMIT 9;
```

## 2/ Affichez les 9 jeux les plus vendus (un jeu est considéré comme vendu, lorsque sa clé primaire est présente dans la table "user_own_game")

```sql
SELECT COUNT(*) AS 'jeux plus vendu',
game.name
FROM `game`
JOIN user_own_game ON user_own_game.game_id = game.id
GROUP BY user_own_game.game_id
ORDER BY COUNT(*) DESC LIMIT 9;

```

## 3/ Affichez les 9 jeux les plus joués (table "user_own_game", colonne "game_time")

```sql
SELECT SUM(user_own_game.game_time) AS 'Temps de jeu total',
game.name
FROM `game`
JOIN user_own_game ON user_own_game.game_id = game.id
GROUP BY user_own_game.game_id
ORDER BY SUM(user_own_game.game_time) DESC LIMIT 9;
```

### 4/ Affichez les 4 derniers commentaires postés, avec seulement ces colonnes : "user.nickname", "game.name", "review.rating", "review.up_vote", "review.down_vote"

```sql
SELECT user.nickname, game.name, review.rating, review.up_vote, review.down_vote, review.created_at
FROM review
JOIN `user` ON user.id = review.user_id
JOIN `game` ON game.id = review.game_id
ORDER BY review.created_at
DESC LIMIT 4;
```

## 5/ Affichez les 9 catégories les plus joués (va probablement falloir chercher dans "user_own_game"...)

```sql
SELECT COUNT(*) AS 'nb', category.name
FROM `category`
JOIN game_category ON game_category.category_id = category.id
JOIN user_own_game ON user_own_game.game_id = game_category.game_id
GROUP BY game_category.category_id
ORDER BY COUNT(*) DESC LIMIT 9;
```

### 6/ Affichez le temps de jeu total par nom de compte (colonne : "user.nickname" et "Temps de jeux total", le temps de jeu se trouve dans : "user_own_game.game_time")

```sql
SELECT SUM(user_own_game.game_time) AS 'temps de jeu', user.nickname
FROM `user_own_game`
JOIN user ON user.id = user_own_game.user_id
GROUP BY user_own_game.user_id
ORDER BY SUM(user_own_game.game_time) DESC;
```

### 7/ Afficher les jeux ayant la catégorie "FPS" et coûtant moins de 30€

```sql
SELECT game.name, game.price, category.name
FROM game
JOIN game_category ON game_category.game_id = game.id
JOIN category ON category.id = game_category.category_id
WHERE category.name = 'FPS' AND game.price < 30;
```

### 8/ Afficher les jeux (table : "game") avec leurs différentes catégories respectives, cependant je veux une ligne par jeux et les différents genres en une seule colonne, que vous renommerez "Genre(s)". (PS : utiliser la fonction "GROUP_CONCAT" pour l'affichage en une seule colonne)

```sql
SELECT game.name, GROUP_CONCAT(category.name SEPARATOR ', ') AS 'Genre(s)'
FROM game
JOIN game_category ON game_category.game_id = game.id
JOIN category ON category.id = game_category.category_id
GROUP BY game.id
ORDER BY game.name;

SELECT game.name, GROUP_CONCAT(category.name SEPARATOR ', ') AS 'Genre(s)'
FROM `game`
JOIN game_category ON game_category.game_id = game.id
JOIN category ON category.id = game_category.category_id
GROUP BY game.name
ORDER BY category.name;
```

### 9/ Affichez le prix total des jeux achetés pour un utilisateur (colonne : "user.nickname" et "Coût total")

```sql
SELECT SUM(game.price) AS 'Coût total', user.nickname
FROM user_own_game
JOIN user ON user.id = user_own_game.user_id
JOIN game ON game.id = user_own_game.game_id
GROUP BY user_own_game.user_id
ORDER BY SUM(game.price) DESC;

```

### 10/ Afficher par année, le nombre de jeu sortis pour chacune d'entre elles, par exemple : "2025 : 0", "2000 : 3", etc

```sql
SELECT COUNT(*) AS 'Nombre de jeux', YEAR(published_at) AS 'Année'
FROM game
GROUP BY YEAR(published_at)
ORDER BY YEAR(published_at);

-- verifier le nombre total de jeux
SELECT SUM(nombre_de_jeux) AS total_jeux
FROM ( SELECT YEAR(published_at) AS annee, COUNT(*) AS nombre_de_jeux
FROM game
GROUP BY annee )
AS jeux_par_annee;
```

### 11/ Afficher les jeux les plus anciens

```sql
SELECT * FROM game
WHERE published_at
ORDER BY published_at ASC;
```

### 12/ Afficher les jeux avec leur note respective (la note se trouve dans la table "review", colonne "rating"), on doit calculer la moyenne des "rating" par jeu

```sql
SELECT game.name, ROUND(AVG(review.rating), 2) AS 'Moyenne des notes'
FROM game
JOIN review ON review.game_id = game.id
GROUP BY game.id
ORDER BY ROUND(AVG(review.rating), 2) DESC;
```

### 13/ Afficher le jeu le moins bien noté (la note la plus basse)

```sql
SELECT game.name, MIN(review.rating) AS 'Note la plus basse'
FROM game
JOIN review ON review.game_id = game.id
GROUP BY game.id
ORDER BY MIN(review.rating) ASC
LIMIT 1;

-- sur la moyenne de tous les jeux
SELECT game.name, ROUND(AVG(review.rating), 2) AS 'Moyenne des notes' FROM game JOIN review ON review.game_id = game.id GROUP BY game.id ORDER BY ROUND(AVG(review.rating), 2) ASC;
```

### 14/ Afficher les jeux dont leur note est supérieure à la moyenne globale

```sql
SELECT game.name, ROUND(AVG(review.rating), 2) AS 'Moyenne des notes'
FROM game
JOIN review ON review.game_id = game.id
GROUP BY game.id
HAVING ROUND(AVG(review.rating), 2) > (
    SELECT ROUND(AVG(rating), 2)
    FROM review
);
```

### 15/ Afficher les utilisateurs n'ayant jamais acheté de jeu

```sql
SELECT *, user.nickname
FROM user_own_game
JOIN user ON user.id = user_own_game.user_id
GROUP BY user_own_game.user_id IS NULL;
```

### 16/ Afficher les utilisateurs ayant acheté au moins un jeu qui n'est pas dans leur langue natale

```sql
SELECT user.nickname, country.name AS 'Langue natale', game.name AS 'Jeu acheté'
FROM user
JOIN user_own_game ON user_own_game.user_id = user.id
JOIN game ON game.id = user_own_game.game_id
JOIN game_country ON game_country.game_id = game.id
JOIN country ON country.id = game_country.country_id
WHERE user.language != country.name
AND user_own_game.game_id IS NOT NULL
ORDER BY user.nickname, game.name;

SELECT user.nickname, country.name AS 'Langue natale', game.name AS 'Jeu acheté',
game_country.game_id AS 'pays du jeu'
FROM user JOIN user_own_game ON user_own_game.user_id = user.id
JOIN game ON game.id = user_own_game.game_id
JOIN game_country ON game_country.game_id = game.id
JOIN country ON country.id = game_country.country_id
WHERE user.country_id != country.name
AND user_own_game.game_id IS NOT NULL ORDER BY user.nickname, game.name;
```

### 17/ Afficher le ratio de présence des utilisateurs par pays

```sql
SELECT country.name AS 'Pays', COUNT(user.id) AS 'Nombre utilisateurs',
       COUNT(user.id) / (SELECT COUNT(*) FROM user) * 100 AS 'Ratio (%)'
       FROM user
       JOIN country ON country.id = user.country_id
       GROUP BY country.name
       ORDER BY COUNT(user.id) DESC;

```

### 18/ Faire une requête qui simule une barre de recherche, elle doit récupérer les informations en provenance des tables "game", "category", "publisher". Par exemple si l'utilisateur tape "pro", on doit faire ressortir tous les jeux ayant spécifiquement le nom "pro" ou ceux qui contiennent la chaine de caractère "pro", pareil pour les gens et éditeurs de jeu. Vous devrez tester votre requête avec une valeur par défaut

```sql
SELECT game.name AS 'Jeu', category.name AS 'Catégorie', publisher.name AS 'Éditeur' FROM game
JOIN game_category ON game_category.game_id = game.id
JOIN category ON category.id = game_category.category_id
JOIN publisher ON publisher.id = game.publisher_id
WHERE game.name LIKE '%arc%' OR category.name LIKE '%arc%' OR publisher.name LIKE '%arc%'
ORDER BY game.name;

```

### 19/ Même question que la 18, mais cette fois en pondérant les résultats pour les trier par pertinence ?

````sql
SELECT game.name AS 'Jeu', category.name AS 'Catégorie', publisher.name AS 'Éditeur',
       (CASE
           WHEN game.name LIKE '%arc%' THEN 3
           WHEN category.name LIKE '%arc%' THEN 2
           WHEN publisher.name LIKE '%arc%' THEN 1
           ELSE 0
       END) AS 'Pertinence'
       FROM game
       JOIN game_category ON game_category.game_id = game.id
         JOIN category ON category.id = game_category.category_id
         JOIN publisher ON publisher.id = game.publisher_id
         WHERE game.name LIKE '%arc%' OR category.name LIKE '%arc%' OR publisher.name LIKE '%arc%'
            ORDER BY `Pertinence` DESC, game.name;
            ```
````
