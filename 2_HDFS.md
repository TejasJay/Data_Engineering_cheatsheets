# **🔥 Step 1: Introduction to Hadoop & HDFS**

### **📌 What is HDFS?**

HDFS (**Hadoop Distributed File System**) is a **fault-tolerant, distributed file system** designed to store **large-scale data** across multiple machines.

✅ **Why use HDFS?**

-   Handles **huge datasets** (terabytes to petabytes).
-   **Scalable** – Adds more nodes as storage grows.
-   **Fault-tolerant** – Replicates data across machines.
* * *

## **🔥 Step 2: HDFS Components**

HDFS has a **Master-Slave Architecture**:

| **Component** | **Function** |
| --- | --- |
| **NameNode (Master)** | Manages metadata (file locations, permissions, replication info). |
| **DataNodes (Slaves)** | Store actual data blocks. |
| **Secondary NameNode** | Helps in metadata checkpointing (not a failover node). |

* * *

## **🔥 Step 3: Install & Start HDFS**

### **✅ 1. Format the NameNode (Prepares HDFS for First-Time Use)**

bash

CopyEdit

`hdfs namenode -format`

📌 **What does this do?**

-   Initializes the **metadata storage** of the NameNode.
-   Deletes old metadata and creates a **fresh file system**.
* * *

### **✅ 2. Start HDFS Services**

bash

CopyEdit

`start-dfs.sh`

📌 **What does this do?**

-   Starts **NameNode (Master)** and **DataNodes (Workers)** in the cluster.
-   Allows files to be stored and retrieved in HDFS.

🔹 **Verify services are running:**

bash

CopyEdit

`jps`

📌 **What does this show?**

-   Lists running Hadoop processes (`NameNode`, `DataNode`, `SecondaryNameNode`).
* * *

### **✅ 3. Check HDFS Cluster Status**

bash

CopyEdit

`hdfs dfsadmin -report`

📌 **What does this do?**

-   Displays **live DataNodes, total storage, free space, and replication factor**.
* * *

# **🔥 Step 4: Basic HDFS Commands (Hands-On)**

📌 **Now let’s create directories, upload files, move, and delete files in HDFS.**

* * *

### **✅ 1. Create Directories in HDFS**

bash

CopyEdit

`hdfs dfs -mkdir /user/your_username`

📌 **What does this do?**

-   Creates a new directory `/user/your_username` inside HDFS.
-   Similar to `mkdir` in Linux, but for HDFS.

bash

CopyEdit

`hdfs dfs -mkdir /user/your_username/input hdfs dfs -mkdir /user/your_username/output`

📌 **Why create multiple directories?**

-   `/input` → Stores raw files.
-   `/output` → Stores processed data.

🔹 **Verify directories were created:**

bash

CopyEdit

`hdfs dfs -ls /user/your_username`

📌 **What does this do?**

-   Lists all files and directories inside `/user/your_username`.
* * *

### **✅ 2. Upload Files to HDFS**

bash

CopyEdit

`echo "Hadoop is a distributed file system" > localfile.txt hdfs dfs -put localfile.txt /user/your_username/input/`

📌 **What does this do?**

-   Creates a sample file `localfile.txt` in local storage.
-   `put` uploads the file to HDFS under `/user/your_username/input/`.

🔹 **Verify file was uploaded:**

bash

CopyEdit

`hdfs dfs -ls /user/your_username/input/`

📌 **What does this do?**

-   Lists all files in `/user/your_username/input/` inside HDFS.
* * *

### **✅ 3. Read Files from HDFS**

bash

CopyEdit

`hdfs dfs -cat /user/your_username/input/localfile.txt`

📌 **What does this do?**

-   Reads and displays the content of `localfile.txt` stored inside HDFS.
-   Similar to `cat` in Linux.
* * *

### **✅ 4. Download Files from HDFS**

bash

CopyEdit

`hdfs dfs -get /user/your_username/input/localfile.txt .`

📌 **What does this do?**

-   Retrieves `localfile.txt` from HDFS and saves it in the **current directory (`.`)**.

bash

CopyEdit

`hdfs dfs -copyToLocal /user/your_username/input/localfile.txt .`

📌 **What’s the difference?**

-   Both `get` and `copyToLocal` copy files from HDFS to the local file system.
-   `get` provides **progress status**, while `copyToLocal` doesn’t.
* * *

### **✅ 5. Rename & Move Files in HDFS**

bash

CopyEdit

`hdfs dfs -mv /user/your_username/input/localfile.txt /user/your_username/input/data.txt`

📌 **What does this do?**

-   Renames `localfile.txt` to `data.txt`.

bash

CopyEdit

`hdfs dfs -mv /user/your_username/input/data.txt /user/your_username/output/`

📌 **What does this do?**

-   Moves `data.txt` from `/input/` to `/output/`.

🔹 **Check if the file was moved:**

bash

CopyEdit

`hdfs dfs -ls /user/your_username/output/`

📌 **What does this do?**

-   Lists the contents of `/output/` to verify the move.
* * *

### **✅ 6. Delete Files & Directories in HDFS**

bash

CopyEdit

`hdfs dfs -rm /user/your_username/output/data.txt`

📌 **What does this do?**

-   Deletes `data.txt` from HDFS.

bash

CopyEdit

`hdfs dfs -rm -r /user/your_username/output`

📌 **What does this do?**

-   Deletes **the entire directory `/output/`** and its contents.
* * *

# **🔥 Step 5: File Permissions & ACLs in HDFS**

📌 **Every file in HDFS has permissions like Linux (`rwx`).**

### **✅ 1. Check File Permissions**

bash

CopyEdit

`hdfs dfs -ls -h /user/your_username/input/`

📌 **What does this do?**

-   Lists files with **ownership, size, and permissions** in human-readable format (`-h`).
* * *

### **✅ 2. Change File Permissions**

bash

CopyEdit

`hdfs dfs -chmod 755 /user/your_username/input/`

📌 **What does this do?**

-   Gives **read, write, and execute (rwx) permissions** to the owner.
-   Gives **read & execute (r-x) permissions** to others.
* * *

### **✅ 3. Change File Ownership**

bash

CopyEdit

`hdfs dfs -chown your_user:your_group /user/your_username/input/`

📌 **What does this do?**

-   Changes **file owner and group ownership** in HDFS.
* * *

### **✅ 4. Set ACLs for Fine-Grained Access**

bash

CopyEdit

`hdfs dfs -setfacl -m user:anotheruser:rwx /user/your_username/input/`

📌 **What does this do?**

-   Grants **full access (rwx)** to `anotheruser`.

bash

CopyEdit

`hdfs dfs -setfacl -b /user/your_username/input/`

📌 **What does this do?**

-   Removes all ACL rules from `/input/`.
* * *


# **🔥 1. How HDFS Stores Data (Block-Based Storage)**

### **📌 Why Blocks?**

HDFS **splits large files** into **fixed-size blocks** to store across multiple machines.

📌 **Default Block Size:**

-   **128MB** (Hadoop 2.x & 3.x, configurable)
-   **64MB** (Hadoop 1.x)

📌 **How File Storage Works in HDFS:** 1️⃣ When a file is uploaded, HDFS **divides it into blocks** (e.g., a **600MB file** → 5 blocks of 128MB each).
2️⃣ Each block is stored on **different DataNodes** to ensure redundancy & fault tolerance.
3️⃣ The **NameNode tracks where each block is stored** in metadata.

💡 **Example: A 500MB file in HDFS (128MB block size)**

| Block | DataNode 1 | DataNode 2 | DataNode 3 |
| --- | --- | --- | --- |
| Block 1 (128MB) | ✅ | ✅ (Replica) | ✅ (Replica) |
| Block 2 (128MB) | ✅ | ✅ (Replica) | ✅ (Replica) |
| Block 3 (128MB) | ✅ | ✅ (Replica) | ✅ (Replica) |
| Block 4 (116MB) | ✅ | ✅ (Replica) | ✅ (Replica) |

📌 **View Block Size Configuration in HDFS**

bash

CopyEdit

`hdfs getconf -confKey dfs.blocksize`

🔹 **Displays the current block size setting in bytes (default: 134217728 for 128MB).**

📌 **View Block Details of a File**

bash

CopyEdit

`hdfs fsck /user/your_username/input/localfile.txt -files -blocks -locations`

🔹 **Shows how HDFS splits a file into blocks and where each block is stored.**

* * *

# **🔥 2. HDFS NameNode & Metadata Management**

### **📌 What is Metadata?**

The **NameNode doesn’t store actual data**—it only keeps a **mapping (metadata) of files to blocks**.

📌 **Metadata is stored in two files:**

| **Metadata File** | **Purpose** |
| --- | --- |
| **fsimage** | A snapshot of the HDFS namespace (file structure, permissions, locations). |
| **edits log** | Tracks recent metadata changes (file creation, deletion, renaming). |

💡 **Example: If you create a file in HDFS…**

-   NameNode **updates `edits log` immediately**.
-   Every few minutes, the **Secondary NameNode merges `fsimage` and `edits log`** into a new `fsimage` file.

📌 **View fsimage & edits log (Hands-On)**

bash

CopyEdit

`ls -lh /usr/local/hadoop/tmp/dfs/name/current/`

🔹 **Shows metadata files (`fsimage`, `edits`, `VERSION`).**

📌 **Check Safe Mode Status (Read-Only Mode for Metadata)**

bash

CopyEdit

`hdfs dfsadmin -safemode get`

🔹 **If enabled, HDFS doesn’t allow writes until block reports are received.**

📌 **Manually Force a Checkpoint (Merge fsimage & edits log)**

bash

CopyEdit

`hdfs secondarynamenode -checkpoint force`

🔹 **Forces the Secondary NameNode to merge metadata.**

* * *

# **🔥 3. HDFS Replication & Fault Tolerance**

### **📌 How HDFS Handles Failures (Replication Mechanism)**

✅ By default, **each block is replicated 3 times** for fault tolerance.
✅ If a **DataNode fails**, HDFS **automatically re-replicates lost blocks**.

📌 **Check the Replication Factor of a File**

bash

CopyEdit

`hdfs fsck /user/your_username/input/localfile.txt -files -blocks`

🔹 **Displays the replication factor of each block.**

📌 **Manually Set Replication Factor**

bash

CopyEdit

`hdfs dfs -setrep -w 2 /user/your_username/input/localfile.txt`

🔹 **Changes replication factor to 2 (default is 3).**

📌 **View Missing Blocks in HDFS**

bash

CopyEdit

`hdfs fsck / -missing`

🔹 **Lists any missing blocks that need replication.**

* * *

# **🔥 4. Rack Awareness in HDFS**

### **📌 What is Rack Awareness?**

HDFS **distributes data across multiple racks** in a data center to **protect against entire rack failures**.

✅ Blocks are stored in **different racks** to **ensure high availability**. ✅ Default policy: **One block replica in a different rack** to avoid total data loss.

💡 **Example: If a Data Center has 3 Racks (`Rack-1`, `Rack-2`, `Rack-3`)**

| Block | DataNode in Rack-1 | DataNode in Rack-2 | DataNode in Rack-3 |
| --- | --- | --- | --- |
| Block 1 | ✅ (Replica 1) | ✅ (Replica 2) | ✅ (Replica 3) |

📌 **Check Rack Awareness Configuration**

bash

CopyEdit

`hdfs getconf -confKey net.topology.script.file.name`

🔹 **Shows the script used for rack topology.**

📌 **View Blocks Placement Across Racks**

bash

CopyEdit

`hdfs fsck / -files -blocks -racks`

🔹 **Shows which racks store each file's blocks.**

* * *

# **🔥 5. Simulating HDFS Failures (Hands-On)**

### **✅ 1. Kill a DataNode to Simulate Failure**

bash

CopyEdit

`jps  # Find the DataNode process ID kill -9 <DataNode_PID>`

🔹 **Kills a DataNode to test HDFS self-healing.**

📌 **Check Missing Blocks After DataNode Failure**

bash

CopyEdit

`hdfs fsck / -files -blocks -locations`

🔹 **Lists blocks that lost replication.**

📌 **Force HDFS to Rebalance & Restore Replication**

bash

CopyEdit

`hdfs balancer`

🔹 **Redistributes blocks to ensure proper replication.**

---

## 🔹 **HDFS Internal Architecture Overview**
HDFS (Hadoop Distributed File System) is a **master-slave architecture** consisting of:  
🔹 **NameNode** (Master) → Manages metadata and file system namespace  
🔹 **DataNodes** (Slaves) → Store actual file blocks and send heartbeats to NameNode  
🔹 **Secondary NameNode** → Assists in checkpointing metadata (not a failover NameNode!)  

---

# 1️⃣ **NameNode & Metadata Management** 🖥️  

### **🔹 What is `fsimage` & `edit logs`?**
📂 **NameNode stores metadata**, such as file locations, permissions, and directory structure. This is stored in two files:  
1️⃣ **fsimage** → A complete snapshot of the file system metadata  
2️⃣ **edit logs** → Records recent changes (file creation, deletion, etc.)  

👉 When the NameNode starts, it **loads fsimage** and **replays edit logs** to reconstruct the latest state of the file system.  

### ✅ **Hands-on Command: Checking Safe Mode**
**Safe Mode** is when the NameNode temporarily **prevents write operations** while waiting for DataNodes to report back.  

🛠️ Check if Safe Mode is ON or OFF:  
```bash
hdfs dfsadmin -safemode get
```
✅ If ON, disable it manually:  
```bash
hdfs dfsadmin -safemode leave
```

### **🔹 How does NameNode handle failure?**
When the **NameNode crashes**, all metadata in RAM is lost. To prevent this:  
✅ **Checkpointing** → Periodically merges `fsimage` & `edit logs` to reduce recovery time.  
✅ **Secondary NameNode (SNN)** → Takes snapshots to help with recovery (but is NOT a failover NameNode).  

🛠️ **Check Last Checkpoint Time**  
```bash
hdfs dfsadmin -fetchImage .
ls -lh fsimage_*
```

---

# 2️⃣ **DataNodes & Storage** 🗄️  

### **🔹 How DataNodes Store Blocks**
🔹 Files are **split into blocks** (default size **128MB or 256MB**)  
🔹 Blocks are stored **across multiple DataNodes**  
🔹 Each block is **replicated** to ensure fault tolerance  

✅ **Check where blocks are stored for a file:**  
```bash
hdfs fsck / -files -blocks -locations
```
👉 This will list **block locations on DataNodes**.  

### **🔹 How Replication Works**
HDFS **replicates blocks (default: 3 copies)** to prevent data loss.  
✅ Check the replication factor:  
```bash
hdfs getconf -confKey dfs.replication
```
✅ Change the replication factor for a file:  
```bash
hdfs dfs -setrep -w 2 /path/to/file
```

### **🔹 How HDFS Balances Data**
If some DataNodes have **too much data**, HDFS **balances** data across the cluster.  
✅ Run the balancer manually:  
```bash
hdfs balancer
```

---

# 3️⃣ **HDFS Read & Write Process** 📥📤  

### **🔹 How Files are Written to HDFS**
📌 When a client writes a file:  
1️⃣ **Splits into blocks** (default: **128MB**)  
2️⃣ **Contact NameNode** → Assigns DataNodes to store each block  
3️⃣ **DataNodes replicate the block** to ensure fault tolerance  

✅ **Check where a file is stored after writing:**  
```bash
hdfs fsck /user/$(whoami)/filename.txt -files -blocks -locations
```

### **🔹 How Files are Read from HDFS**
📌 When a client reads a file:  
1️⃣ **Contacts NameNode** → Gets block locations  
2️⃣ **Reads data directly from DataNodes** (closest copy first)  
3️⃣ **Reconstructs the file on the client side**  

✅ **Read file content from HDFS:**  
```bash
hdfs dfs -cat /user/$(whoami)/filename.txt
```

---

# 4️⃣ **Rack Awareness in HDFS** 🌍  

### **🔹 Why is Rack Awareness Important?**
🛠️ HDFS ensures data **is not lost even if an entire rack fails** by distributing replicas across different racks.  
1️⃣ NameNode **assigns blocks to different racks** to ensure redundancy.  
2️⃣ Default policy → **2 copies on the same rack, 1 copy on a different rack**.  

✅ **Check rack awareness settings:**  
```bash
hdfs getconf -confKey net.topology.script.file.name
```

### **🔹 How to Configure Rack Awareness**
🛠️ Edit the Hadoop rack script:  
```bash
nano $HADOOP_HOME/etc/hadoop/topology.script
```
Add:  
```bash
#!/bin/bash
# Assign rack based on hostname
if [[ $1 == "datanode1" ]]; then
  echo "/rack1"
elif [[ $1 == "datanode2" ]]; then
  echo "/rack2"
else
  echo "/default-rack"
fi
```
✅ **Make it executable:**  
```bash
chmod +x $HADOOP_HOME/etc/hadoop/topology.script
```

---
