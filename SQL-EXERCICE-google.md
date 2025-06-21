# Explication de la base

```sql

SELECT *
FROM lpecom_livres;
```

```sql
SELECT *
FROM lpecom_livres
WHERE prix > 20;
```

```sql
SELECT *
FROM lpecom_livres
ORDER BY prix DESC;
```

## Quelle requête utiliser pour récupérer le prix du livre le plus élevé de la table lpecom_livres ?

```sql

SELECT MAX(prix)
FROM lpecom_livres;
```

## Quelle requête utiliser pour récupérer les livres de la table lpecom_livres qui ont un prix compris entre 20 et 22 ?

```sql

SELECT *
FROM lpecom_livres
WHERE prix BETWEEN 20 AND 22;
```

## Quelle requête utiliser pour récupérer tous les livres de la table lpecom_livres à lexception de celui portant la valeur pour la colonne isbn_10 : 2092589547 ?

```sql
SELECT *
FROM lpecom_livres
WHERE isbn_10 != 2092589547;
```

## Quelle requête utiliser pour récupérer le prix du livre le moins élevé de la table lpecom_livres en renommant la colonne dans les résultats par minus ?

```sql

SELECT MIN(prix) AS minus
FROM lpecom_livres;
```

## OFFSET permet de ne pas retourner une partie des résultats de la reqûete. Cette commande sutilise uniquement en complément de la commande LIMIT. Par exemple, nous souhaitons sélectionner uniquement les 2 premiers résultats sans le tout premier

Quelle requête utiliser pour sélectionner uniquement les 3 premiers résultats sans le tout premier de la table lpecom_livres ?

```sql

SELECT *
FROM lpecom_livres
LIMIT 3 OFFSET 1;
```

## Quelle requête utiliser pour afficher l id des étudiants qui ont participés à au moins un examen ?

```sql

SELECT lpecom_examens.id_etudiant
FROM `lpecom_etudiants`
JOIN lpecom_examens ON lpecom_examens.id_etudiant = lpecom_etudiants.id_etudiant
ORDER BY lpecom_examens.id_etudiant IS NOT NULL;

SELECT DISTINCT id_etudiant
FROM lpecom_examens;
```

## Quelle requête utiliser pour compter le nombre d étudiants qui ont participés à au moins un examen ?

```sql

SELECT COUNT(DISTINCT id_etudiant)
FROM lpecom_examens;
```

## Quelle requête utiliser pour calculer la moyenne de l examen portant l id : 45

```sql

SELECT AVG(note)
FROM lpecom_examens
WHERE id_examen = 45;
```

## Quelle requête utiliser pour récupérer la meilleure note de l examen portant l id : 87 ?

```sql

SELECT MAX(note)
FROM lpecom_examens
WHERE id_examen = 87;
```

## Quelle requête utiliser pour afficher l id des étudiants qui ont eu plus de 11 à l examen 45 ou plus de 12 à l examen 87 ?

```sql

SELECT DISTINCT id_etudiant
FROM lpecom_examens
WHERE (id_examen = 45 AND note > 11)
OR (id_examen = 87 AND note > 12);
```

## Quelle requête utiliser pour afficher l, id des étudiants qui ont eu plus de 11 à l examen 45 ou plus de 12 à l examen 87 ?

```sql

SELECT DISTINCT id_etudiant
FROM lpecom_examens
WHERE (id_examen = 45 AND note > 11)
OR (id_examen = 87 AND note > 12);
```

## Quelle requête utiliser pour afficher tous les enregistrement de la table lpecom_examens avec en plus, si c est possible, le prenom et le nom de l étudiant ?

```sql

SELECT ex.*, et.prenom, et.nom
FROM lpecom_examens ex
LEFT JOIN lpecom_etudiants et ON ex.id_etudiant = et.id_etudiant;

SELECT lpecom_examens.*, lpecom_etudiants.prenom, lpecom_etudiants.nom
FROM lpecom_examens
LEFT JOIN lpecom_etudiants ON lpecom_examens.id_etudiant = lpecom_etudiants.id_etudiant;

SELECT lpecom_examens.*, lpecom_etudiants.prenom, lpecom_etudiants.nom
FROM `lpecom_examens`
LEFT JOIN lpecom_etudiants ON lpecom_etudiants.id_etudiant = lpecom_examens.id_etudiant;
```

## Quelle requête utiliser pour afficher les enregistrement de la table lpecom_examens avec le prenom et le nom de l étudiant, uniquement quand les étudiants sont présents dans la table lpecom_etudiants ?

```sql

SELECT ex.*, et.prenom, et.nom
FROM lpecom_examens ex
LEFT JOIN lpecom_etudiants et ON ex.id_etudiant = et.id_etudiant
WHERE et.id_etudiant IS NOT NULL;

SELECT lpecom_examens.*, lpecom_etudiants.prenom, lpecom_etudiants.nom
FROM lpecom_examens
LEFT JOIN lpecom_etudiants ON lpecom_examens.id_etudiant = lpecom_etudiants.id_etudiant
WHERE lpecom_etudiants.id_etudiant IS NOT NULL

SELECT ex.*, et.prenom, et.nom
FROM lpecom_examens ex
INNER JOIN lpecom_etudiants et ON ex.id_etudiant = et.id_etudiant;

```

## Quelle requête utiliser pour afficher uniquement le nom et le prenom de létudiant avec l id : 30 avec la moyenne de ses deux examens dans une colonne moyenne ?

```sql
SELECT et.prenom, et.nom, AVG(ex.note) AS moyenne
FROM lpecom_etudiants et
JOIN lpecom_examens ex ON et.id_etudiant = ex.id_etudiant
WHERE et.id_etudiant = 30
GROUP BY et.id_etudiant;

SELECT nom, prenom, AVG(lpecom_examens.note) AS 'moyenne'
FROM `lpecom_etudiants`
JOIN lpecom_examens ON lpecom_examens.id_etudiant = lpecom_etudiants.id_etudiant
WHERE lpecom_etudiants.id_etudiant = 30
GROUP BY lpecom_etudiants.id_etudiant;

SELECT et.prenom, et.nom, AVG(ex.note) as moyenne
FROM lpecom_examens ex
INNER JOIN lpecom_etudiants et ON ex.id_etudiant = et.id_etudiant
WHERE et.id_etudiant = 30;
```

## Quelle requête utiliser pour afficher les 3 meilleures examens, du meilleur au moins bon, avec le prenom et le nom de l'étudiant associé ?

```sql
SELECT et.prenom, et.nom, ex.note
FROM lpecom_examens ex
INNER JOIN lpecom_etudiants et ON ex.id_etudiant = et.id_etudiant
ORDER BY ex.note DESC
LIMIT 3;

SELECT *
FROM `lpecom_examens`
INNER JOIN lpecom_etudiants ON lpecom_etudiants.id_etudiant = lpecom_examens.id_etudiant ORDER BY lpecom_examens.note DESC
LIMIT 3;
```

## Quelle requête utiliser pour retrouver la ville qui possède les coordonnées GPS suivantes : 48.66913724637683, 1.87586057971015 ? toujours sur région Ile -de-France id = 6

```sql
SELECT * FROM lpecom_cities
WHERE gps_lat = 48.66913724637683 AND gps_lng = 1.87586057971015;

```

## Sans jointure, quelle requête utiliser pour calculer le nombre de villes que compte le département de l'Essonne ?

```sql
SELECT id, code FROM lpecom_departments WHERE name = 'Essonne';
id = 92 code = 91

SELECT COUNT(*)
FROM `lpecom_cities`
WHERE department_code = 91;
```

## Sans jointure, quelle requête utiliser pour calculer le nombre de villes en Île-de-France se terminant par "-le-Roi" ?

```sql
SELECT COUNT(*)
FROM `lpecom_cities`
WHERE name LIKE '%-le-Roi';
```

## Combien de villes possèdent le code postal (zip_code) 77320 ? Renommez la colonne de résultat n_cities

```sql
SELECT COUNT(*) AS 'n_cities'
FROM `lpecom_cities`
WHERE zip_code = 77320;
```

## Sans jointure, quelle requête utiliser pour calculer le nombre de villes commençant par "Saint-" en Seine-et-Marne ?

```sql
SELECT * FROM `lpecom_departments`
WHERE slug = 'seine et marne';
code = 77

SELECT COUNT(*) FROM lpecom_cities WHERE name LIKE "SAINT-%" AND department_code = 77;
```

## Quelles villes possèdent un code postal (zip_code) compris entre 77210 et 77810 ?

```sql
SELECT *
FROM `lpecom_cities`
WHERE zip_code
BETWEEN 77210 AND 77810;
```

## Sans jointure, quelles sont les deux villes de Seine-et-Marne à avoir le code postal (zip_code) le plus grand ?

```sql
SELECT * FROM `lpecom_cities`
WHERE department_code = 77
ORDER BY zip_code DESC
LIMIT 2;
```

## Quel est le code postal zip_code le plus grand de la table lpecom_cities ?

```sql
SELECT MAX(zip_code)
FROM `lpecom_cities`
WHERE zip_code;
```

## Avec un seul WHERE et aucun OR, quelle est la requête permettant d'afficher les départements des régions ayant le code suivant : 75, 27, 53, 84 et 93 ? Le résultat doit afficher le nom du département ainsi que le nom et le slug de la région associée

```sql
SELECT lpecom_departments.name AS 'département', lpecom_regions.name, lpecom_regions.slug
FROM `lpecom_departments`
INNER JOIN lpecom_regions ON lpecom_regions.id = lpecom_departments.id
WHERE lpecom_departments.region_code IN (75, 27, 53, 84, 93);
```

## Quelle requête utiliser pour obtenir en résultat, les noms de la région, du département et de chaque ville du département ayant pour code 77 ?

```sql
SELECT lpecom_regions.name AS 'région', lpecom_departments.name AS 'département', lpecom_cities.name AS 'ville'
FROM `lpecom_regions`
INNER JOIN lpecom_departments ON lpecom_departments.region_code = lpecom_regions.id
INNER JOIN lpecom_cities ON lpecom_cities.department_code = lpecom_departments.code
WHERE lpecom_departments.code = 77;
```
