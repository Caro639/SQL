# SQL

## Exercice 1 Pokemon

```sql

1 SELECT name FROM `pokemon` WHERE is_default = 0;
219 pokemon

2 SELECT id, name, AVG(hp) FROM `pokemon` GROUP BY name;

3 SELECT name, weight FROM `pokemon` ORDER BY weight DESC LIMIT 1;

4 SELECT name, height FROM `pokemon` ORDER BY height LIMIT 1;

5 SELECT name, spd FROM `pokemon` ORDER BY spd LIMIT 1;

6 SELECT name, atk FROM `pokemon` WHERE atk >= 150; = 24 pokemon

7 SELECT name, (hp + atk + def + spa + spd + spe) AS somme FROM pokemon;

8 SELECT name, slug, name_api FROM `pokemon` WHERE slug = name_api;

9 SELECT name FROM `pokemon` WHERE atk > 150;

10 SELECT name FROM `pokemon` WHERE name LIKE '%Mew%';

11 SELECT \* FROM `pokemon` WHERE LENGTH(name) <= 3;

12 SELECT \* FROM `pokemon` WHERE LENGTH(name) ORDER BY LENGTH(name) DESC LIMIT 1;

13 SELECT \* FROM pokemon ORDER BY (hp + atk + def + spa + spd + spe) DESC LIMIT 1;

14 SELECT \* FROM `pokemon` WHERE base_experience ORDER BY base_experience LIMIT 1;

15 SELECT _, CONCAT(ROUND((base_experience / (hp + atk + def + spa + spd + spe )) _ 100, 2), '%') AS efficace FROM `pokemon` ORDER BY ROUND((base_experience / (hp + atk + def + spa + spd + spe )) \* 100, 2);

## Exercice 2 students numéro 1

1 SELECT first_name, last_name FROM students;

2 SELECT \*, height FROM students WHERE height <= 160 ORDER BY sex;

3 SELECT \*, height FROM `students` ORDER BY height;

4 SELECT sex, AVG(height) FROM `students` WHERE sex LIKE '%M%' GROUP BY sex;

5 SELECT COUNT(\*) FROM `students` WHERE sex LIKE '%M%';

SELECT COUNT(\*) FROM `students` WHERE sex LIKE '%F%';

6 SELECT COUNT(sex) FROM `students` WHERE sex LIKE '%M%' GROUP BY height <= 160;

7 SELECT height, COUNT(*) AS nb_etudiants FROM `students` GROUP BY height HAVING COUNT(*) > 1;

8 SELECT \* FROM `students` WHERE height BETWEEN 170 AND 190;

9 SELECT \* FROM `students` WHERE height IN (160, 170, 180, 190);

## Exercice 2 students numéro 2

1 INSERT INTO students (last_name, first_name, height, sex)
VALUES ('Parker', 'Antony', 199, 'M');

2 SELECT \*, CONCAT(ROUND(height / 100, 2), 'm') AS height_m FROM students;

3 UPDATE students SET height = 189 WHERE last_name = 'Parker';

4 DELETE FROM students WHERE first_name = 'Maxine';

5 SELECT \*,

CASE
    WHEN sex = 'F' THEN 'Femme'
    WHEN sex = 'M' THEN 'Homme'
    ELSE sex
  END AS sexe_libelle
FROM students;

6 ,

SELECT *,
 CONCAT(ROUND(height / 100, 2), 'm') AS height_m,
 CASE
   WHEN sex = 'F' THEN 'Femme'
   WHEN sex = 'M' THEN 'Homme'
   ELSE sex
 END AS sexe_libelle
FROM students;
```
