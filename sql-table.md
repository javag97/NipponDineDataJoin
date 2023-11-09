Create table in SQL

```SQL
CREATE TABLE restaurants (
    id SERIAL PRIMARY KEY,
    name TEXT,
    address TEXT,
    avg_rating FLOAT,
    zip CHAR(5),
    categories TEXT[],
    city TEXT
);
```

Copy file into Postgres

```SQL
COPY restaurants (name, address, avg_rating, zip, categories, city)
FROM '/Users/javi/projects/school/assignment-3/nourish_public_ca_business.csv'
DELIMITER ','
CSV HEADER;
```


Exploration Japanese query

```
SELECT *
FROM restaurants
WHERE 
	(zip IN ('92064', '92074', '92145', '92131', '92126', '92129', '92128', '92065', '92040', '92071'))
	AND (
		'Japan' = ANY(categories) 
        OR 'Sushi' = ANY(categories)
	);
```


JSON path


Query 1

```
$.Cities[?(@['City Name'] == 'Poway')].Neighborhoods[*].['ZIP Codes']
```

Result

```
[
  "92128, 92129",
  "92025, 92029, 92064, 92067, 92127, 92128",
  "92064, 92131, 92145",
  "92128, 92129, 92131"
]
```

Query 2

```
$.Cities[?(@['City Name'] == 'Poway')]['ZIP Codes']
```

Result

```
[
  "92025, 92064, 92065, 92131"
]
```