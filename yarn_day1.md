# **🔥 YARN Cheat Sheet**

## **1️⃣ Basic YARN Commands**

| **Command** | **Description** |
| --- | --- |
| `yarn node -list` | Show all active NodeManagers & available resources (memory, CPU) |
| `yarn application -list` | List all running and completed jobs |
| `yarn application -status <APPLICATION_ID>` | Show detailed status of a job |
| `yarn logs -applicationId <APPLICATION_ID>` | View logs of a specific job |
| `yarn scheduler` | Show current YARN scheduler type (FIFO, Fair, Capacity) |
| `yarn queue -status <QUEUE_NAME>` | Check queue capacity, used resources, priority |

* * *

## **2️⃣ YARN Resource Allocation (Memory & CPU)**

💡 **Control how much memory & CPU YARN assigns to jobs**

Edit **`yarn-site.xml`** (`nano $HADOOP_HOME/etc/hadoop/yarn-site.xml`)

```xml
<!-- Set total memory & CPU for YARN -->
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>8192</value>  <!-- Total 8GB for YARN -->
</property>

<property>
    <name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>4</value>  <!-- 4 CPU cores available -->
</property>

<!-- Set memory per container -->
<property>
    <name>yarn.scheduler.minimum-allocation-mb</name>
    <value>1024</value>  <!-- Minimum 1GB per container -->
</property>

<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>4096</value>  <!-- Maximum 4GB per container -->
</property>
```

📌 **Restart YARN after changes:**

```bash
stop-yarn.sh && start-yarn.sh
```

* * *

## **3️⃣ Optimizing MapReduce Jobs**

Edit **`mapred-site.xml`** (`nano $HADOOP_HOME/etc/hadoop/mapred-site.xml`)

```xml
<property>
    <name>mapreduce.map.memory.mb</name>
    <value>2048</value>  <!-- Mappers get 2GB each -->
</property>

<property>
    <name>mapreduce.reduce.memory.mb</name>
    <value>2048</value>  <!-- Reducers get 2GB each -->
</property>

<property>
    <name>mapreduce.map.cpu.vcores</name>
    <value>2</value>  <!-- Mappers use 2 CPU cores each -->
</property>

<property>
    <name>mapreduce.reduce.cpu.vcores</name>
    <value>2</value>  <!-- Reducers use 2 CPU cores each -->
</property>
```

* * *

## **4️⃣ YARN Scheduling (Capacity Scheduler)**

💡 **Ensure fair resource sharing for multiple users**

Edit **`capacity-scheduler.xml`** (`nano $HADOOP_HOME/etc/hadoop/capacity-scheduler.xml`)

```xml
<property>
    <name>yarn.scheduler.capacity.root.default.capacity</name>
    <value>50</value>  <!-- Default queue gets 50% of resources -->
</property>

<property>
    <name>yarn.scheduler.capacity.root.analytics.capacity</name>
    <value>50</value>  <!-- Analytics queue gets 50% of resources -->
</property>

<property>
    <name>yarn.scheduler.capacity.root.default.maximum-applications</name>
    <value>5</value>  <!-- Default queue can run max 5 jobs at a time -->
</property>
```

📌 **Submit jobs to a specific queue:**

```bash
hadoop jar my-job.jar -D mapreduce.job.queuename=analytics
```

* * *

## **5️⃣ YARN Preemption (Reallocating Resources Automatically)**

💡 **Ensure urgent jobs don’t get stuck behind long-running ones**

Edit **`capacity-scheduler.xml`**:

```xml
<property>
    <name>yarn.scheduler.capacity.preemption.enabled</name>
    <value>true</value>  <!-- Enable preemption -->
</property>

<property>
    <name>yarn.scheduler.capacity.preemption.monitor.interval-ms</name>
    <value>5000</value>  <!-- Check for preemption every 5 seconds -->
</property>
```

📌 **Now, YARN will automatically reallocate resources if needed.**

* * *

## **6️⃣ Node Labels (Assign Jobs to Specific Nodes)**

💡 **Ensure specific jobs run on specific hardware (e.g., GPUs, SSDs)**

### **🛠️ Enable Node Labels**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.node-labels.enabled</name>
    <value>true</value>  <!-- Enable Node Labels -->
</property>
```

### **🛠️ Add Node Labels**

```bash
yarn rmadmin -addToClusterNodeLabels "fastssd(gpu=false), gpucluster(gpu=true)"
```

Assign nodes to labels:

```bash
yarn rmadmin -replaceLabelsOnNode "worker-node1=fastssd"
yarn rmadmin -replaceLabelsOnNode "worker-node2=gpucluster"
```

📌 **Submit a job to a specific node label:**

```bash
hadoop jar my-job.jar -D yarn.app.mapreduce.am.node-label-expression=fastssd
```

* * *

## **7️⃣ Debugging YARN Issues**

💡 **Fix common failures by checking logs**

### **🛠️ View Logs for a Failed Job**

```bash
yarn logs -applicationId <APPLICATION_ID>
```

### **🛠️ Job Stuck in ACCEPTED State**

```bash
yarn application -list  # Check if resources are available
```

👉 If all CPU/memory is used, kill a job to free up resources:

```bash
yarn application -kill <APPLICATION_ID>
```

### **🛠️ Check Real-Time Cluster Resource Usage**

```bash
yarn node -list
```

📌 **See memory & CPU usage per node.**

* * *

## **✅ Recap: What You Just Learned**

✅ **Manage memory & CPU allocation (`yarn-site.xml`)**
✅ **Optimize MapReduce jobs (`mapred-site.xml`)**
✅ **Set up Capacity Scheduler for multi-user fairness (`capacity-scheduler.xml`)**
✅ **Enable Preemption for urgent jobs**
✅ **Use Node Labels to assign jobs to specific nodes**
✅ **Monitor & debug failed jobs using `yarn logs`**

* * *

