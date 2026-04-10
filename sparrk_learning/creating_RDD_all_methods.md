<img width="1440" height="1398" alt="image" src="https://github.com/user-attachments/assets/13e63728-6691-48aa-88ca-b3a5c767fde2" />
---

## 🧠 All RDD Creation Methods — Deep with Code

---

### Method 1 — `parallelize()` · For learning

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("RDD").master("local[2]").getOrCreate()
spark.sparkContext.setLogLevel("ERROR")
sc = spark.sparkContext

# 1. Numbers
rdd1 = sc.parallelize([1, 2, 3, 4, 5])
print(rdd1.collect())   # [1, 2, 3, 4, 5]

# 2. Range of numbers (like Python range)
rdd2 = sc.parallelize(range(1, 11))
print(rdd2.collect())   # [1,2,3,4,5,6,7,8,9,10]

# 3. Strings
rdd3 = sc.parallelize(["Chennai", "Mumbai", "Delhi", "Bangalore"])
print(rdd3.collect())   # ['Chennai', 'Mumbai', 'Delhi', 'Bangalore']

# 4. Key-Value pairs (Paired RDD)
rdd4 = sc.parallelize([("Anto", 95), ("Ravi", 87), ("Kumar", 76)])
print(rdd4.collect())   # [('Anto', 95), ('Ravi', 87), ('Kumar', 76)]

# 5. Control how many partitions
rdd5 = sc.parallelize([1,2,3,4,5,6,7,8,9,10], numSlices=3)
print("Partitions:", rdd5.getNumPartitions())  # 3
```

---

### Method 2 — `textFile()` · For real files

First create a file called `students.txt` in your project folder:
```
Anto,95,Chennai
Ravi,87,Mumbai
Kumar,76,Delhi
Diana,91,Bangalore
```

Now read it with Spark:

```python
# Read file — each LINE becomes one item in RDD
rdd = sc.textFile("students.txt")

print("Total lines:", rdd.count())       # 4
print("First line:", rdd.first())        # Anto,95,Chennai
print("All lines:", rdd.collect())
# ['Anto,95,Chennai', 'Ravi,87,Mumbai', 'Kumar,76,Delhi', 'Diana,91,Bangalore']

# Now split each line into parts
split_rdd = rdd.map(lambda line: line.split(","))
print(split_rdd.collect())
# [['Anto','95','Chennai'], ['Ravi','87','Mumbai'], ...]

# Get only names
names = rdd.map(lambda line: line.split(",")[0])
print(names.collect())   # ['Anto', 'Ravi', 'Kumar', 'Diana']

# Get only scores as numbers
scores = rdd.map(lambda line: int(line.split(",")[1]))
print(scores.collect())  # [95, 87, 76, 91]
print("Highest score:", scores.max())    # 95
print("Average score:", scores.mean())  # 87.25
```

---

### Method 3 — From another RDD · Most common

```python
rdd = sc.parallelize([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])

# Each transformation creates a NEW RDD
rdd_doubled  = rdd.map(lambda x: x * 2)
rdd_evens    = rdd.filter(lambda x: x % 2 == 0)
rdd_big      = rdd.filter(lambda x: x > 5)

print("Original:", rdd.collect())
# [1,2,3,4,5,6,7,8,9,10]

print("Doubled:", rdd_doubled.collect())
# [2,4,6,8,10,12,14,16,18,20]

print("Evens only:", rdd_evens.collect())
# [2,4,6,8,10]

print("Greater than 5:", rdd_big.collect())
# [6,7,8,9,10]
```

---

### 🔥 Real World Example — Sales data

Create `sales.txt`:
```
Anto,Electronics,15000
Ravi,Clothing,2500
Kumar,Electronics,18000
Diana,Groceries,500
Priya,Electronics,22000
```

```python
sales = sc.textFile("sales.txt")

# Split each line
data = sales.map(lambda x: x.split(","))
# [['Anto','Electronics','15000'], ...]

# Get only Electronics sales
electronics = sales \
    .map(lambda x: x.split(",")) \
    .filter(lambda x: x[1] == "Electronics")

print("Electronics sales:")
for row in electronics.collect():
    print(f"  {row[0]} → ₹{row[2]}")

# Output:
# Electronics sales:
#   Anto → ₹15000
#   Kumar → ₹18000
#   Priya → ₹22000

# Total electronics revenue
total = sales \
    .map(lambda x: x.split(",")) \
    .filter(lambda x: x[1] == "Electronics") \
    .map(lambda x: int(x[2])) \
    .sum()

print(f"Total Electronics: ₹{total}")
# Total Electronics: ₹55000
```

---

### ⚠️ Important rules about creating RDD

```
Rule 1: parallelize() → only for small test data
        Real data is always from files/DB/Kafka

Rule 2: textFile() → each LINE = one RDD item
        You must split/parse each line yourself

Rule 3: Every transformation = NEW RDD
        Original RDD never changes (immutable)

Rule 4: getNumPartitions() → check how data is split
        More partitions = more parallel processing
```

---

### 🎯 One line understanding:

> **4 ways to create RDD: parallelize() for testing, textFile() for files, transformation for processing, external sources for real company data**

---

### 💬 Interview answer:

*"RDDs can be created in multiple ways. The parallelize method creates an RDD from an existing Python collection, mainly used for testing. The textFile method reads data from files where each line becomes one RDD element. RDDs can also be created by applying transformations like map or filter on existing RDDs, which is the most common approach. In production, RDDs are typically created from external sources like databases, cloud storage, or Kafka streams."*

---

### ✅ Practice now — try all 3:

```python
# Practice 1 — parallelize
rdd1 = sc.parallelize([10, 20, 30, 40, 50])
print("Max:", rdd1.max())
print("Count:", rdd1.count())

# Practice 2 — create students.txt file first, then:
rdd2 = sc.textFile("students.txt")
print("Lines:", rdd2.count())
print("First:", rdd2.first())

# Practice 3 — from transformation
rdd3 = sc.parallelize(range(1, 21))
evens = rdd3.filter(lambda x: x % 2 == 0)
print("Even numbers:", evens.collect())
```

---
