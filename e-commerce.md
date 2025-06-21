# e-commerce exercices

## 1/ Affichez les 9 produits les plus vendus, avec seulement les colonnes "product.id", "product.label", "product.price", "product.reference"

```sql
SELECT SUM(quantity), product.id, product.reference, product.label, product.price
FROM `product_order`
JOIN product ON product.id = product_order.product_id
GROUP BY product_id
ORDER BY SUM(quantity) DESC
LIMIT 9;
```

### 2/ Affichez les 4 catégories les plus vendus, avec seulement les colonnes "category.label", "category.id"

```sql
SELECT SUM(product_order.quantity) AS 'total', category.label
FROM `category`
JOIN product_categories ON product_categories.categories_id = category.id
JOIN product ON product.id = product_categories.products_id
JOIN product_order ON product_order.product_id = product.id

GROUP BY category.label, category.id
ORDER BY SUM(product_order.quantity) DESC
LIMIT 4;
```

### 3/ Affichez les 9 produits vendus dernièrement, avec seulement les colonnes "product.id", "product.label", "product.price", "product.reference"

```sql
SELECT product.id,
       product.label,
       product.price,
       product.reference,
       cmd.created_at
FROM `product`
JOIN product_order ON product_order.product_id = product.id
JOIN `order` cmd ON cmd.id =  product_order.order_id
ORDER BY cmd.created_at DESC
LIMIT 9;
```

### 4/ Affichez les 4 derniers commentaires postés, avec seulement les colonnes : "product.id", "user.id", "user.username", "product.label", "comment.rating", "comment.created_at"

```sql
SELECT product.id, user.id, user.username, product.label, comment.rating, comment.created_at
FROM `comment`
LEFT JOIN user ON user.id = comment.user_id
LEFT JOIN product ON product.id = comment.product_id
ORDER BY comment.created_at DESC
LIMIT 4;
```

### 5/ Affichez les informations de l'utilisateur avec ses adresses, avec seulement les colonnes : "user.id", "user.username", "user.first_name", "user.last_name", "address.\*"

```sql
SELECT user.id, user.username, user.first_name, user.last_name, address.*
FROM `user`
JOIN address ON address.user_id = user.id;
```

### 6/ Affichez le ou les utilisateurs ayant renseigné le plus d'adresses

```sql
SELECT COUNT(*), user.id,
        user.username,
        user.first_name,
        user.last_name,
FROM `user` JOIN address
ON address.user_id = user.id
GROUP BY user_id
HAVING COUNT(*) > 1
ORDER BY COUNT(*) DESC;
```

### 7/ Afficher les produits de la catégorie "Books" et de ses sous-catégories

```sql
SELECT product.id, product.label, product.price, product.reference
FROM `product`
JOIN product_categories ON product_categories.products_id = product.id
JOIN category ON category.id = product_categories.categories_id
WHERE category.label = 'Books' OR category.parent_id = (SELECT id FROM category WHERE label = 'Books');
```
