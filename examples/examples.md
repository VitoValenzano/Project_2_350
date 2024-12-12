# Interacting with Apache Ignite using Python

## Node Connection
Start by connecting to cluster(i.e. the node you created in the [installation guide](https://github.com/VitoValenzano/Project_2_350/blob/main/docs/guide.md))
```
from pyignite import Client

client = Client()
client.connect('127.0.0.1', 10800)
```

You may(and often times should) connect to multiple nodes at once as one whole network. This would look something like this, with the parameters obviously depending on the addresses of the nodes you're interested in
```
nodes = [
    ('127.0.0.1', 10800),
    ('217.29.2.1', 10800),
    ('200.10.33.1', 10800),
]

client = Client(timeout=40.0)
client.connect(nodes)
```

## Caches
As you saw in the installation guide, a cache must be created to store frequently-used data for quick retrieval
```
my_cache = client.create_cache('my cache')
```

To add to a cache, you call the .put(key, value) 
```
my_cache.put('key1', 1234)
```

To retrieve a value from the cache, you use the corresponding key value 
```
print(my_cache.get(1))
```

You may optionally use get_all() to select multiple values from their keys
```
result = my_cache.get_all([
    'key1',
    'key2',
    'key3',
])
```

To view all elements in the cache, use the .scan() function
```
result = my_cache.scan()

for k, v in result:
    print(k, v)
```

You may also cast result to a dictionary and print that way, but this is a stupid idea with very large datasets due to memory usage

# Executing SQL Statements
Pyignite supports the usage of SQL statements using the .sql() function

To start, we need to create a table in our node

```
CREATE_TABLE = '''CREATE TABLE Fruit (
    ID INT(11),
    Name CHAR(50),
    Color CHAR(20),
    Taste CHAR(30),
    Calories INT(11),
    PRIMARY KEY (ID)
) WITH "affinityKey=ID"'''

client.sql(CREATE_TABLE)

CREATE_INDEX = '''CREATE INDEX idx_color ON Fruit (Color)'''

client.sql(CREATE_INDEX)
```

This table is obviously going to be a list of fruits and some of their properties. So, we must now populate the table like so
```
INSERT_QUERY = '''INSERT INTO Fruit(
    ID, Name, Color, Taste, Calories
) VALUES (?, ?, ?, ?, ?)'''

FRUIT_DATA = [
    [1, 'Apple', 'Red', 'Sweet', 95],
    [2, 'Banana', 'Yellow', 'Sweet', 105],
    [3, 'Orange', 'Orange', 'Citrus', 62],
    [4, 'Strawberry', 'Red', 'Sweet', 33],
    [5, 'Lemon', 'Yellow', 'Sour', 17],
    [6, 'Blueberry', 'Blue', 'Sweet', 84],
    [7, 'Watermelon', 'Green', 'Sweet', 85],
    [8, 'Grapefruit', 'Pink', 'Bitter', 52],
]

for row in FRUIT_DATA:
    client.sql(INSERT_QUERY, query_args=row)
```

You would then conduct a select query, for example, in the same way

```
query = "SELECT * FROM Fruit"

fruits = client.sql(query)
for fruit in fruits:
    print(*fruit)

```

There are much more complex applications of SQL to interact with the cache through in the [Ignite documentation](https://ignite.apache.org/docs/2.16.0/sql-reference/), but this is the basic functionality

