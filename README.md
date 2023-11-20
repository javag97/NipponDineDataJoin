# Group 4: List the Japanese restaurants in cities near Poway.

## **PART A** write the corresponding SQL and JSONPath queries for their respective questions.

Poway zip codes: 92064, 92074.

nearby: 92145, 92131, 92126, 92129, 92128, 92065, 92040, 92071

## Create table in SQL

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
FROM 'INSERT/PATH/TO/PROJECT/assignment-3/nourish_public_ca_business.csv'
DELIMITER ','
CSV HEADER;
```

## Exploration Japanese query

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
*Note that this should logically equivalent as looking for cities near Poway.* Using these zip codes, I want to look for Japanese restaurants. Within the dataset, I found instances of "Sushi" or "Japan". In reality, we should expand this to include inclusionary terms that could singify cuisines within Japan. I will refer to all of these inclusionary terms as Nippon (any term to designate Japanese in origin).


#### Query 3: Look for Japenese restaurants in nearby zip codes. 


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

Result: Query complete 00:00:00.151

|id |name                            |address                                                                       |avg_rating|zip  |categories                                                       |city    |
|---|--------------------------------|------------------------------------------------------------------------------|----------|-----|-----------------------------------------------------------------|--------|

This produces an empty set, so I will expand this.

#### Query 4: Look for Japenese restaurants in entire database. 

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

Result: Query complete 00:00:00.152

|id |name                            |address                                                                       |avg_rating|zip  |categories                                                       |city    |
|---|--------------------------------|------------------------------------------------------------------------------|----------|-----|-----------------------------------------------------------------|--------|
|50 |Shogun of La Jolla              |Shogun of La Jolla, 9500 Gilman Dr, La Jolla, CA 92093                        |3.4       |92093|{"Japanese restaurant"}                                          |La Jolla|
|157|James' Place Prime Seafood Sushi|James' Place Prime Seafood Sushi, 2910 La Jolla Village Dr, La Jolla, CA 92093|4.5       |92093|{"Sushi restaurant",Restaurant}                                  |La Jolla|
|244|The Bistro at the Strand        |The Bistro at the Strand, 9500 Gilman Dr, La Jolla, CA 92093                  |3.8       |92093|{"Asian fusion restaurant","Asian restaurant","Sushi restaurant"}|La Jolla|


## **PART B** write Python code to implement the "join" operation in the query. We will expect your code to be "generic" in the sense that we should be able to use it to join the outputs of any JSONPath and SQL queries. Your code should make use of data structures like hash tables (or something comparable) and operations like sorting, probing, merging. However, the specific choice of data structures and operations is up to you.



## **PART C** write code to generate the output the result to the user as a Python list. Notice that for group 3, each list item will have a pair of values

