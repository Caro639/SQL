# Modifier une table avec des clés étrangères

```sql

ALTER TABLE models
MODIFY category_id INT NOT NULL,
ADD CONSTRAINT categories
FOREIGN KEY (category_id) REFERENCES category(id);

ALTER TABLE listings
ADD COLUMN seller_id INT NOT NULL,
ADD CONSTRAINT sellers
FOREIGN KEY (seller_id) REFERENCES sellers(id);
```
