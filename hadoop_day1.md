# **🔥 Hadoop Cheat Sheet**

## **1️⃣ HDFS Commands (Hadoop Distributed File System)**

💡 **Manage files & directories in HDFS**

| **Command** | **Description** |
| --- | --- |
| `hdfs dfsadmin -report` | Check HDFS status (live DataNodes, storage, etc.) |
| `hdfs dfs -mkdir /user/tejasjay` | Create a directory in HDFS |
| `hdfs dfs -ls /user/tejasjay/` | List files in an HDFS directory |
| `hdfs dfs -put myfile.txt /user/tejasjay/` | Upload a file to HDFS |
| `hdfs dfs -cat /user/tejasjay/myfile.txt` | View the contents of a file in HDFS |
| `hdfs dfs -cp /user/tejasjay/myfile.txt /user/tejasjay/myfile_copy.txt` | Copy a file in HDFS |
| `hdfs dfs -rm /user/tejasjay/myfile.txt` | Delete a file in HDFS |
| `hdfs dfs -rm -r /user/tejasjay/output_dir` | Delete a directory in HDFS (recursively) |

* * *

## **2️⃣ Running Python MapReduce with Hadoop Streaming**

💡 **Run Python-based MapReduce jobs on Hadoop**

### **📌 Command to Run MapReduce Job**

```bash
hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
    -D mapreduce.job.reduces=1 \
    -files mapper.py,reducer.py \
    -input /user/tejasjay/moby_dick.txt \
    -output /user/tejasjay/wordcount \
    -mapper "python3 mapper.py" \
    -reducer "python3 reducer.py"
```

### **📌 Mapper (`mapper.py`)**

```python
#!/usr/bin/env python3
import sys

for line in sys.stdin:
    words = line.strip().split()
    for word in words:
        print(f"{word}\t1")  # Output (word, 1)
```

### **📌 Reducer (`reducer.py`)**

```python
#!/usr/bin/env python3
import sys

current_word = None
current_count = 0

for line in sys.stdin:
    word, count = line.strip().split("\t")
    count = int(count)
    if word == current_word:
        current_count += count
    else:
        if current_word:
            print(f"{current_word}\t{current_count}")
        current_word = word
        current_count = count

if current_word:
    print(f"{current_word}\t{current_count}")
```

### **📌 View Output in HDFS**

```bash
hdfs dfs -ls /user/tejasjay/wordcount
hdfs dfs -cat /user/tejasjay/wordcount/part-00000 | head -20
```

* * *

## **3️⃣ Customizing MapReduce (Filtering Stop Words)**

### **📌 Stop Words File (`stopwords.txt`)**

```
the
is
and
to
a
in
of
for
on
```

### **📌 Modified Mapper (`mapper.py`)**

```python
#!/usr/bin/env python3
import sys

# Load stop words
stop_words = set()
with open("stopwords.txt", "r") as f:
    for line in f:
        stop_words.add(line.strip())

for line in sys.stdin:
    words = line.strip().lower().split()
    for word in words:
        if word not in stop_words:  # Ignore stop words
            print(f"{word}\t1")
```

### **📌 Run MapReduce with Stop Words Filter**

```bash
hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
    -D mapreduce.job.reduces=1 \
    -files mapper.py,reducer.py,stopwords.txt \
    -input /user/tejasjay/moby_dick.txt \
    -output /user/tejasjay/wordcount_filtered \
    -mapper "python3 mapper.py" \
    -reducer "python3 reducer.py"
```

* * *

## **4️⃣ Chaining MapReduce Jobs (Sorting Word Counts)**

### **📌 Sorting Mapper (`sort_mapper.py`)**

```python
#!/usr/bin/env python3
import sys

for line in sys.stdin:
    word, count = line.strip().split("\t")
    print(f"{count}\t{word}")  # Swap order for sorting
```

### **📌 Sorting Reducer (`sort_reducer.py`)**

```python
#!/usr/bin/env python3
import sys

for line in sys.stdin:
    count, word = line.strip().split("\t")
    print(f"{word}\t{count}")
```

### **📌 Run Sorting MapReduce Job**

```bash
hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
    -D stream.num.map.output.key.fields=2 \
    -D mapreduce.job.reduces=1 \
    -files sort_mapper.py,sort_reducer.py \
    -input /user/tejasjay/wordcount_filtered/part-00000 \
    -output /user/tejasjay/wordcount_sorted \
    -mapper "python3 sort_mapper.py" \
    -reducer "python3 sort_reducer.py"
```

* * *

## **5️⃣ Debugging Tips**

💡 **Fix common Hadoop issues**

| **Issue** | **Solution** |
| --- | --- |
| `No live datanodes` | Restart HDFS: `stop-dfs.sh && start-dfs.sh` |
| `Output directory already exists` | Delete it first: `hdfs dfs -rm -r /user/tejasjay/output_dir` |
| `Task failed task_xxx` | Check logs: `yarn logs -applicationId <APPLICATION_ID>` |
| `Permission denied` | Change ownership: `sudo chown -R $(whoami):staff /tmp/hadoop-$(whoami)/` |
| `File not found` | Verify with `hdfs dfs -ls /user/tejasjay/` |

* * *


