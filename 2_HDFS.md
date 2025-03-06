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

* * *

## 🔹 **1\. NameNode & Metadata**

The **NameNode** is the **master node** that manages metadata and file system operations.

✅ **Key Responsibilities**
🔹 Maintains the **file system namespace** (directory structure).
🔹 Stores **metadata** (mapping of file names to block locations).
🔹 Manages **DataNodes** and keeps track of active nodes.
🔹 Handles **replication** and ensures fault tolerance.

✅ **Important Files in NameNode Storage**
📂 `fsimage` – A snapshot of the entire file system metadata.
📂 `edits` – A log of recent file system changes since the last `fsimage` save.
📂 `fstime` – Stores the last checkpoint timestamp.

* * *

## 🔹 **2\. DataNodes & Storage**

The **DataNodes** are the **worker nodes** that store the actual data blocks.

✅ **Key Responsibilities**
🔹 Store **HDFS data blocks** on the local file system.
🔹 Periodically send **heartbeat** signals to the NameNode.
🔹 Perform **read/write operations** on request.
🔹 Replicate data blocks based on the replication factor.

✅ **How DataNodes Manage Data Blocks**
📦 Data is split into **blocks** (default: 128MB).
🔁 Each block is replicated **(default: 3 copies)** across multiple DataNodes.
🔍 The **Block Report** is sent to the NameNode to verify block locations.

* * *

## 🔹 **3\. HDFS Read & Write Process**

Understanding **how HDFS reads and writes files** is key to mastering its architecture.

✅ **📝 Write Process**
1️⃣ Client contacts **NameNode** to request file creation.
2️⃣ NameNode **allocates blocks** and selects DataNodes for storage.
3️⃣ Client **writes data** to the selected DataNodes in a pipeline.
4️⃣ DataNodes replicate the blocks **(default: 3 copies)**.
5️⃣ Once all blocks are written, the **NameNode updates metadata**.

✅ **📖 Read Process**
1️⃣ Client requests a file from the **NameNode**.
2️⃣ NameNode returns a **list of DataNodes** storing the file blocks.
3️⃣ Client reads the blocks **directly from DataNodes** in parallel.
4️⃣ Once all blocks are retrieved, the **file is reconstructed**.

* * *

## 🔹 **4\. Rack Awareness**

HDFS follows a **rack-aware block placement policy** to improve fault tolerance.

✅ **What is Rack Awareness?**
🔹 HDFS **assigns racks** to DataNodes.
🔹 NameNode ensures that **replicated blocks are placed on different racks**.
🔹 This prevents data loss in case of **rack failure**.

✅ **Default Replication Strategy (Replication Factor = 3)**
📌 Block 1 → **Rack 1, Node A**
📌 Block 1 Copy → **Rack 2, Node B**
📌 Block 1 Copy → **Rack 1, Node C**

This ensures that at least **one copy exists on a different rack** for redundancy.

* * *

## 🔹 **5\. Key Configuration Files**

HDFS behavior is controlled by key configuration files:

📂 **core-site.xml**

-   Defines **HDFS URI** (e.g., `hdfs://localhost:9000/`)
-   Configures **I/O operations** like buffer size.

📂 **hdfs-site.xml**

-   Configures **replication factor**, block size, and NameNode directories.
-   Example property:

    xml

    CopyEdit

    `<property>   <name>dfs.replication</name>   <value>3</value> </property>`

📂 **yarn-site.xml**

-   Configures **YARN resource management**.

📂 **mapred-site.xml**

-   Defines **MapReduce settings**.
* * *

✅ **Step 1: Check if HDFS is Running**
Run the following command to verify if **NameNode and DataNodes** are running:

bash

CopyEdit

`jps`

👉 You should see processes like `NameNode`, `DataNode`, `SecondaryNameNode`, `ResourceManager`, and `NodeManager`.
❌ If **NameNode is missing**, try:

bash

CopyEdit

`hdfs --daemon start namenode`

* * *

✅ **Step 2: Check HDFS Health**

bash

CopyEdit

`hdfs dfsadmin -report`

👉 This should list **available DataNodes, storage capacity, and replication info**.
💡 Look for **Number of DataNodes** (should be at least **1**).

* * *

✅ **Step 3: Create a Directory in HDFS**
Let’s create a directory named **test\_dir** in HDFS:

bash

CopyEdit

`hdfs dfs -mkdir /user/$(whoami)/test_dir`

👉 Verify if it was created:

bash

CopyEdit

`hdfs dfs -ls /user/$(whoami)`

* * *

✅ **Step 4: Upload a File to HDFS**
📁 Create a test file locally:

bash

CopyEdit

`echo "Hello, HDFS!" > testfile.txt`

📤 Upload it to HDFS:

bash

CopyEdit

`hdfs dfs -put testfile.txt /user/$(whoami)/test_dir/`

👉 Verify if the file is in HDFS:

bash

CopyEdit

`hdfs dfs -ls /user/$(whoami)/test_dir/`

* * *

✅ **Step 5: Read the File from HDFS**

bash

CopyEdit

`hdfs dfs -cat /user/$(whoami)/test_dir/testfile.txt`

👉 You should see:

CopyEdit

`Hello, HDFS!`

* * *

✅ **Step 6: Check Block Information**
To check where the file is stored in HDFS:

bash

CopyEdit

`hdfs fsck /user/$(whoami)/test_dir/testfile.txt -files -blocks -locations`

👉 This will show **block details** and which DataNodes store them.

* * *

✅ **Step 7: Delete the File and Directory**
🗑️ Remove the file:

bash

CopyEdit

`hdfs dfs -rm /user/$(whoami)/test_dir/testfile.txt`

🗂️ Remove the directory:

bash

CopyEdit

`hdfs dfs -rmdir /user/$(whoami)/test_dir`


