# Create table in SQL

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


# Exploration Japanese query


### JSON path

#### Query 1: Zip codes within Poway 

```
$.Cities[?(@['City Name'] == 'Poway')]['ZIP Codes']
```

Result

```
[
  "92025, 92064, 92065, 92131"
]
```


#### Query 2: Zip codes of cities around Poway

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

Using these zip codes, I want to look for Japanese restaurants. Within the dataset, I found instances of "Sushi" or "Japan". In reality, we should expand this to include inclusionary terms that could singify cuisines within Japan. I will refer to all of these inclusionary terms as Nippon (any term to designate Japanese in origin).


```
SELECT *
FROM restaurants
WHERE 
	(zip IN ('92128', '92129', '92025', '92029', '92064','92067', '92127', '92128', '92064', '92131', '92145', '92128', '92129', '92131', '92025', '92064', '92065', '92131'))
	AND (
		'Japan' = ANY(categories) 
        OR 'Sushi' = ANY(categories)
	);
```

```
SELECT *
FROM restaurants
WHERE EXISTS (
    SELECT *
    FROM unnest(categories) AS category
    WHERE category ILIKE '%sushi%'
       OR category ILIKE '%japan%'
       OR category ILIKE '%ramen%'
);
```