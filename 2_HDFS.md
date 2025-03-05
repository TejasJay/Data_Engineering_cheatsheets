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


