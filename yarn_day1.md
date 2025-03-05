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

### **🚀 Complete YARN Advanced Tuning Cheat Sheet**

✅ **Auto-Scaling Nodes**
✅ **Disk Isolation & I/O Optimization**
✅ **CPU Cgroups for Resource Control**
✅ **Container Caching for Faster Execution**
✅ **Network Bandwidth Control**
✅ **GPU Scheduling for AI/ML**
✅ **Federated YARN for Multi-Cluster Scaling**
✅ **YARN on Kubernetes for Containerized Workloads**
✅ **YARN Log Aggregation & Performance Monitoring**

* * *

# **🔥 1. YARN Auto-Scaling (Dynamically Add & Remove Nodes)**

📌 **Problem:** Fixed YARN cluster size leads to resource waste or slow job execution.
👉 **Solution:** **Enable auto-scaling** to dynamically add/remove nodes based on cluster load.

### **🛠️ Step 1.1: Configure Dynamic Node Registration**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.resourcemanager.nodes.include-path</name>
    <value>/etc/hadoop/nodes.include</value>  <!-- Allowed nodes -->
</property>

<property>
    <name>yarn.resourcemanager.nodes.exclude-path</name>
    <value>/etc/hadoop/nodes.exclude</value>  <!-- Nodes to remove -->
</property>
```

### **🛠️ Step 1.2: Add & Remove Nodes Dynamically**

-   **Add a new node:**

    ```bash
    echo "worker-node-4" >> /etc/hadoop/nodes.include
    yarn rmadmin -refreshNodes
    ```

-   **Remove an idle node:**

    ```bash
    echo "worker-node-2" >> /etc/hadoop/nodes.exclude
    yarn rmadmin -refreshNodes
    ```

### **🛠️ Step 1.3: Automate Scaling Based on CPU/Memory Usage**

Create a script:

```bash
#!/bin/bash
UTILIZATION=$(yarn node -list | awk '/Memory Used/ {print $4}' | sed 's/%//')

if [ "$UTILIZATION" -gt "80" ]; then
    echo "worker-node-5" >> /etc/hadoop/nodes.include
    yarn rmadmin -refreshNodes
elif [ "$UTILIZATION" -lt "30" ]; then
    echo "worker-node-3" >> /etc/hadoop/nodes.exclude
    yarn rmadmin -refreshNodes
fi
```

📌 **Run this every 5 minutes using `crontab`.**

* * *

# **🔥 2. Disk Isolation & I/O Optimization**

📌 **Problem:** Multiple YARN jobs compete for disk I/O, causing performance issues.
👉 **Solution:** **Distribute I/O across multiple disks.**

### **🛠️ Step 2.1: Assign Multiple Local Disks**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.nodemanager.local-dirs</name>
    <value>/mnt/disk1/yarn,/mnt/disk2/yarn</value>
</property>

<property>
    <name>yarn.nodemanager.log-dirs</name>
    <value>/mnt/disk1/logs,/mnt/disk2/logs</value>
</property>
```

### **🛠️ Step 2.2: Optimize MapReduce Temporary Storage**

Edit **`mapred-site.xml`**:

```xml
<property>
    <name>mapreduce.cluster.local.dir</name>
    <value>/mnt/disk1/mr-temp,/mnt/disk2/mr-temp</value>
</property>
```

📌 **Now, jobs use multiple disks instead of a single one.**

* * *

# **🔥 3. CPU Cgroups (Prevent CPU Overuse)**

📌 **Problem:** A single job can consume all CPU, slowing down other jobs.
👉 **Solution:** **Use CPU Cgroups to enforce CPU limits.**

### **🛠️ Step 3.1: Enable Cgroups in YARN**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.nodemanager.linux-container-executor.resources-handler.class</name>
    <value>org.apache.hadoop.yarn.server.nodemanager.util.CgroupsLCEResourcesHandler</value>
</property>
```

### **🛠️ Step 3.2: Set CPU Limits for Jobs**

Edit **`capacity-scheduler.xml`**:

```xml
<property>
    <name>yarn.scheduler.capacity.root.default.maximum-allocation-vcores</name>
    <value>2</value>  <!-- Limit jobs to max 2 CPU cores -->
</property>
```

* * *

# **🔥 4. Container Caching (Reduce Startup Overhead)**

📌 **Problem:** Jobs reload dependencies every time, slowing execution.
👉 **Solution:** **Cache containers to speed up execution.**

### **🛠️ Step 4.1: Enable Container Caching**

Edit **`mapred-site.xml`**:

```xml
<property>
    <name>mapreduce.job.jvm.numtasks</name>
    <value>-1</value>  <!-- Reuse JVM for multiple tasks -->
</property>
```

* * *

# **🔥 5. Network Bandwidth Control**

📌 **Problem:** Some jobs consume all network bandwidth, slowing others.
👉 **Solution:** **Limit network bandwidth per job.**

### **🛠️ Step 5.1: Enable Bandwidth Enforcement**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.nodemanager.resource.network-bandwidth-mbps</name>
    <value>1000</value>  <!-- Max bandwidth per node (1000 Mbps) -->
</property>
```

* * *

# **🔥 6. GPU Scheduling for AI/ML**

📌 **Problem:** YARN does not natively schedule GPUs.
👉 **Solution:** **Enable GPU scheduling.**

### **🛠️ Step 6.1: Enable GPU Resource Plugin**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.nodemanager.resource-plugins</name>
    <value>gpu</value>
</property>
```

### **🛠️ Step 6.2: Assign GPU Nodes Using Labels**

```bash
yarn rmadmin -addToClusterNodeLabels "gpu-node(gpu=true)"
yarn rmadmin -replaceLabelsOnNode "worker-node3=gpu-node"
```

* * *

# **🔥 7. Federated YARN (Scale Across Multiple Clusters)**

📌 **Problem:** A single YARN cluster has limited capacity.
👉 **Solution:** **Combine multiple YARN clusters.**

### **🛠️ Step 7.1: Enable Federation**

Edit **`yarn-site.xml`**:

```xml
<property>
    <name>yarn.federation.enabled</name>
    <value>true</value>
</property>
```

* * *

# **🔥 8. Running YARN on Kubernetes**

📌 **Problem:** Modern workloads prefer containerized environments.
👉 **Solution:** **Run YARN inside Kubernetes.**

### **🛠️ Step 8.1: Deploy YARN ResourceManager on Kubernetes**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: yarn-resourcemanager
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: resourcemanager
        image: apache/hadoop:latest
        args: ["yarn", "resourcemanager"]
```

* * *

### **✅ Recap: What You Just Learned**

✅ **YARN Auto-Scaling**
✅ **Disk I/O Optimization**
✅ **CPU Isolation using Cgroups**
✅ **GPU Scheduling for AI Workloads**
✅ **Federated YARN for Large-Scale Clusters**
✅ **Running YARN inside Kubernetes**

* * *



