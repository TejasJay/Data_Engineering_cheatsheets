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

### **🚀 Full YARN Advanced Tuning Cheat Sheet **  

✅ **Auto-Scaling Nodes**  
✅ **Disk Isolation & I/O Optimization**  
✅ **CPU Cgroups for Resource Control**  
✅ **Container Caching for Faster Execution**  
✅ **Network Bandwidth Control**  
✅ **GPU Scheduling for AI/ML**  
✅ **Federated YARN for Multi-Cluster Scaling**  
✅ **YARN on Kubernetes for Containerized Workloads**  
✅ **YARN Log Aggregation & Performance Monitoring**  

---

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
- **Add a new node:**  
  ```bash
  echo "worker-node-4" >> /etc/hadoop/nodes.include
  yarn rmadmin -refreshNodes
  ```
- **Remove an idle node:**  
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

---

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

---

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

---

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

📌 **Now, YARN will reuse existing JVMs instead of launching a new one for every job, reducing startup time.**  

---

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

📌 **Now, each NodeManager is limited to a max of 1000 Mbps network usage, preventing bandwidth hogging by a single job.**  

---

# **🔥 6. GPU Scheduling for AI/ML**
📌 **Problem:** YARN does not natively schedule GPUs.  
👉 **Solution:** **Enable GPU scheduling** to efficiently allocate GPU resources for AI/ML workloads.  

### **🛠️ Step 6.1: Enable GPU Resource Plugin**
Edit **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.nodemanager.resource-plugins</name>
    <value>gpu</value>  <!-- Enable GPU plugin -->
</property>

<property>
    <name>yarn.nodemanager.resource-plugins.gpu.allowed-gpu-devices</name>
    <value>0,1</value>  <!-- Allow GPUs 0 and 1 -->
</property>

<property>
    <name>yarn.nodemanager.resource-plugins.gpu.path-to-discovery-executables</name>
    <value>/usr/bin/nvidia-smi</value>  <!-- Path to GPU detection -->
</property>

<property>
    <name>yarn.nodemanager.resource-plugins.gpu.gpu-exclusivity-enabled</name>
    <value>true</value>  <!-- Ensure exclusive GPU access per job -->
</property>
```
📌 **Now, YARN will detect and manage GPUs.**  

---

### **🛠️ Step 6.2: Assign GPU Nodes Using Labels**
```bash
yarn rmadmin -addToClusterNodeLabels "gpu-node(gpu=true)"
yarn rmadmin -replaceLabelsOnNode "worker-node3=gpu-node"
```
📌 **Now, only `worker-node3` will be used for GPU workloads.**  

👉 **To check labels, run:**  
```bash
yarn node -list -showDetails
```

---

### **🛠️ Step 6.3: Configure GPU Allocation Per Container**
Edit **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.resource-types</name>
    <value>yarn.io/gpu</value>  <!-- Define GPU as a resource type -->
</property>

<property>
    <name>yarn.nodemanager.resource.yarn.io/gpu</name>
    <value>2</value>  <!-- Maximum 2 GPUs per node -->
</property>
```
📌 **Now, each NodeManager can allocate up to 2 GPUs per job.**  

---

### **🛠️ Step 6.4: Submit AI/ML Workloads to GPU Nodes**
```bash
hadoop jar my-ai-job.jar -D yarn.app.mapreduce.am.node-label-expression=gpu-node
```
📌 **Now, the job will only run on `gpu-node` labeled machines.**  

---

### **🛠️ Step 6.5: Use Docker for AI/ML Jobs**
If running AI/ML workloads inside a **Docker container**, use:  
```bash
yarn container-exec -image tensorflow/tensorflow:latest -script "/train.py"
```
📌 **Now, YARN runs the AI/ML job inside a Docker container with GPU acceleration.**  

---

# **🔥 7. Federated YARN (Scale Across Multiple Clusters)**
📌 **Problem:** A single YARN cluster has limited capacity.  
👉 **Solution:** **Combine multiple YARN clusters into one large virtual cluster.**  

### **🛠️ Step 7.1: Enable Federation**
Edit **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.federation.enabled</name>
    <value>true</value>
</property>

<property>
    <name>yarn.federation.subcluster-id</name>
    <value>cluster-1</value>  <!-- Unique ID for this cluster -->
</property>

<property>
    <name>yarn.federation.policy-manager</name>
    <value>org.apache.hadoop.yarn.server.federation.policies.amrmproxy.LocalityMulticastPolicy</value>
</property>
```
📌 **Now, this cluster is part of a larger YARN federation.**  

---

### **🛠️ Step 7.2: Connect Multiple Clusters**
On **each cluster**, add the **Federation Inter-Cluster Communication** settings:  
```xml
<property>
    <name>yarn.federation.router.webapp.address</name>
    <value>cluster-1:8088,cluster-2:8088</value>  <!-- List of clusters -->
</property>

<property>
    <name>yarn.federation.amrmproxy.webapp.address</name>
    <value>cluster-1:8089,cluster-2:8089</value>  <!-- Load balance across clusters -->
</property>
```
📌 **Now, multiple YARN clusters work together as one.**  

---

# **🔥 8. Running YARN on Kubernetes**
📌 **Problem:** Modern workloads prefer containerized environments.  
👉 **Solution:** **Run YARN inside Kubernetes.**  

### **🛠️ Step 8.1: Enable Kubernetes as a YARN Resource Manager**
Edit **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.scheduler.capacity.root.default.maximum-allocation-vcores</name>
    <value>4</value>
</property>

<property>
    <name>yarn.nodemanager.runtime.linux.docker.enabled</name>
    <value>true</value>  <!-- Enable Docker for YARN containers -->
</property>
```
📌 **Now, YARN can run jobs inside Docker containers.**  

---

### **🛠️ Step 8.2: Deploy YARN on Kubernetes**
1️⃣ Install **Kubernetes CLI (`kubectl`)**:  
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

2️⃣ Deploy a **YARN ResourceManager pod**:  
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
3️⃣ Deploy a **YARN NodeManager pod**:  
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: yarn-nodemanager
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: nodemanager
        image: apache/hadoop:latest
        args: ["yarn", "nodemanager"]
```
4️⃣ Deploy on Kubernetes:  
```bash
kubectl apply -f yarn-resourcemanager.yaml
kubectl apply -f yarn-nodemanager.yaml
```
📌 **Now, YARN runs inside Kubernetes!**  

---

# **🔥 9. Monitoring & Debugging Performance in YARN**
📌 **Why Monitor YARN?**  
- Identify **slow jobs** and **resource bottlenecks**  
- Debug **failed jobs** using logs  
- Optimize **resource allocation**  

---

### **🛠️ Step 9.1: Check Running & Completed Jobs**
To list **all running applications**, use:  
```bash
yarn application -list
```
📌 **Shows the list of active applications in YARN.**  

To list **all completed applications**, use:  
```bash
yarn application -list -appStates FINISHED
```

To get **detailed information about a specific job**, use:  
```bash
yarn application -status <APPLICATION_ID>
```
📌 **Replace `<APPLICATION_ID>` with the actual application ID.**  

---

### **🛠️ Step 9.2: View YARN Job Logs**
If a job **fails**, check its logs:  
```bash
yarn logs -applicationId <APPLICATION_ID>
```
📌 **This retrieves the logs of the specified job.**  

👉 **If no logs are found, enable YARN log aggregation** (see next step).  

---

### **🛠️ Step 9.3: Enable Log Aggregation in YARN**
If logs are missing, enable log aggregation in **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>  <!-- Enable centralized logging -->
</property>

<property>
    <name>yarn.nodemanager.remote-app-log-dir</name>
    <value>/logs/yarn</value>  <!-- HDFS directory for logs -->
</property>

<property>
    <name>yarn.log-aggregation.retain-seconds</name>
    <value>604800</value>  <!-- Keep logs for 7 days -->
</property>
```
📌 **Now, all logs will be stored in HDFS (`/logs/yarn`).**  

👉 **Create the log directory in HDFS:**  
```bash
hdfs dfs -mkdir -p /logs/yarn
hdfs dfs -chown yarn:hadoop /logs/yarn
```
Restart YARN to apply changes:  
```bash
stop-yarn.sh && start-yarn.sh
```
Now, re-run:  
```bash
yarn logs -applicationId <APPLICATION_ID>
```
Logs should now be visible.  

---

### **🛠️ Step 9.4: Monitor Cluster Resource Usage**
To **check resource usage per node**, use:  
```bash
yarn node -list -showDetails
```
📌 **Shows CPU, memory, and disk usage for each node.**  

To **check memory & CPU usage of running jobs**, use:  
```bash
yarn top
```
📌 **Similar to `top` in Linux, but for YARN jobs.**  

---

### **🛠️ Step 9.5: Debugging Failed Jobs**
If a job **fails**, follow these steps:  
1️⃣ Run:  
   ```bash
   yarn logs -applicationId <APPLICATION_ID> | grep ERROR
   ```
   📌 **Finds errors in job logs.**  

2️⃣ Check for **"Killed" or "OOM"** errors:  
   ```bash
   yarn logs -applicationId <APPLICATION_ID> | grep "Killed"
   ```
   📌 **If found, increase memory allocation (next step).**  

---

### **🛠️ Step 9.6: Fix "Out of Memory" (OOM) Issues**
If a job **fails due to memory limits**, increase YARN memory settings.  

Edit **`yarn-site.xml`**:  
```xml
<property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>8192</value>  <!-- Increase total memory per node -->
</property>

<property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>8192</value>  <!-- Maximum allocation per container -->
</property>
```
📌 **Now, jobs have more memory available.**  

👉 Restart YARN to apply changes:  
```bash
stop-yarn.sh && start-yarn.sh
```

---

# **🔥 10. Performance Optimization Summary**
📌 **Final tuning strategies for better efficiency.**  

| **Optimization Area**   | **Best Practices** |
|-------------------------|--------------------|
| **Auto-Scaling Nodes**  | Dynamically add/remove nodes based on CPU & memory usage. |
| **Disk Isolation**      | Use multiple local disks for YARN logs & temp storage. |
| **CPU Limits (Cgroups)** | Set vCore limits to prevent one job from using all CPU. |
| **Container Caching**   | Reuse JVMs instead of launching a new one every time. |
| **Network Bandwidth Control** | Limit bandwidth per node to prevent slowdowns. |
| **GPU Scheduling**      | Enable `yarn.io/gpu` resource type for AI/ML workloads. |
| **Federated YARN**      | Scale across multiple clusters for massive jobs. |
| **Kubernetes Integration** | Run YARN jobs inside Kubernetes for modern workloads. |
| **Log Aggregation**     | Store logs in HDFS (`/logs/yarn`) for easy debugging. |

---




