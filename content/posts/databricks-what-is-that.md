+++
date = '2025-12-25T18:00:14+08:00'
draft = false
title = 'Databricks: What Is That?'
tags = ["Databricks", "Spark", "Python", "Pandas", "Data Engineering"]
+++

For a long time, **Python + pandas** was more than enough for me.

Read some data, process it, send/ save the result somewhere.

Simple. Familiar. Productive.

Until one day… it wasn’t.

-- 

### My Usual Flow: Python + Pandas

My default approach was always the same:
1. Use pandas to read data
2. Apply business logic
3. Push results to another system

It worked well, especially for:
- Small to medium datasets
- One-off jobs
- Quick experiments

If you’ve written backend scripts or data utilities in Python, this probably feels familiar.

--

### When Pandas Starts to Hurt
As data grew, so did the problems:
- 🚨 Data no longer fits nicely into memory
- 🚨 Scripts become slower as data grows
- 🚨 Running multiple jobs at the same time becomes painful
- 🚨 “Which machine should this run on?” suddenly matters
- 🚨 Sharing scripts with teammates isn’t straightforward

At some point, you realize:
> Pandas is great — but it’s still single-machine thinking.

This is where Databricks entered my life.

--

### Enter Databricks (Without the Hype)
I didn’t move to Databricks because I wanted to “do big data”.

I moved because I wanted:
- To keep writing Python
- To stop worrying about machine size
- To process larger datasets safely
- To schedule jobs without duct tape

Databricks is essentially:
- A managed platform for Apache Spark
- With notebooks
- With scheduling
- With collaboration built in

You write code, Databricks worries about the rest.

--

### A Typical Flow (From My Actual Work)
Here’s what my day-to-day flow looks like now:
1. 📚 Data already exists in the Databricks Catalog
2. 📓 I create a Python notebook
3. 🔄 Read data using Spark
4. 🧠 Apply business logic
5. 📤 Publish results to SQS
6. ⏰ Schedule it to run every 3 hours

No servers to provision. No EC2 sizing debates.

-- 

#### Reading Data with Spark (Feels Familiar)
Instead of Pandas, I now use PySpark:
```python
df = spark.read.table("catalog_name.schema_name.source_table")
``` 
Under the hood, Spark:
- Splits data
- Processes it in parallel
- Handles datasets much larger than memory

--

#### Doing Business Logic (Still Just Python)

```python
from pyspark.sql.functions import col

processed_df = (
    df
    .filter(col("status") == "ACTIVE")
    .withColumn("total_amount", col("price") * col("quantity"))
)
```

--

#### The rest
After processing, I can:
- Write results back to tables
- Send data to external systems (like SQS)
- Schedule notebooks to run automatically

--

### Why Spark Beats Pandas Here (Simply Put)
| Pandas | Spark (via Databricks) |
|---|---|---|
| Single-machine | Distributed across many machines |
| Limited by RAM | Scales with cluster size |
| Manual scheduling | Built-in job scheduling |
| Great for small data | Handles big data seamlessly |

--

I:
- Still write Python
- Still think in business logic

Just stopped worrying about scale and infrastructure

`Databricks` + `Spark` let me grow without rewriting how I think.

If you’re comfortable with Python and Pandas, this transition is far less scary than it sounds.
