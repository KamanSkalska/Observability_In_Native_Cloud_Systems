# Multi-Service Networks

## **1. Introduction**
The goal of this project is to explore and test selected areas of observability in cloud-native environments using the OpenTelemetry toolset (Mimir, Grafana). For this purpose, students are required to set up a Kubernetes cluster in a chosen environment and configure tools necessary for analyzing its capabilities.

---

## **2. Machine Configuration**
For the first phase of the project, the cloud service provider Google Cloud Platform (GCP) was used. Through Compute Engine, three virtual machines were created to form the cluster's backbone (one master node and two worker nodes).

### **2.1 Cluster Parameters**
Each node is based on the same virtual machine with the following specifications:
- **Machine type:** e2-medium  
- **Location:** us-central1-f  
- **Disk:** Ubuntu-22.04-Jammy  
- **Disk size:** 10GB  
- **CPU count:** 2  
- **Architecture:** x86/64  

**Cluster view in Google Cloud Platform:**  
![GCP Project Cloud Engine View](1_GCP.png)

### **2.2 Cluster Network**
Each virtual machine is available in two networks: external and internal. The internal network was created to facilitate communication between machines. IP addresses in this network are assigned dynamically and are associated with each machine's `ens4` interface.

---

## **3. Task 1 - Setting Up the Environment on Virtual Machines**

### **3.1 Installation of Required Components**
Each virtual machine was set up with the necessary environment to host the cluster. The following components were installed:

- `kubectl` - Kubernetes CLI
- `kubeadm` - Cluster initialization
- `kubelet` - Node agent
- `docker` - Container runtime
- `Cilium v1.16.5` - Networking and security

All Kubernetes components are version **1.29**. Additional tools, such as OpenTelemetry (Mimir for monitoring and Grafana for data visualization), are planned for installation.

### **3.2 Verification of Cluster Parameters and Connectivity**
To ensure the cluster was set up correctly, the following parameters were checked:

- **Cluster information**  
  ![Cluster Info](1_cluster_info.png)
  
- **Cilium status**  
  ![Cilium Status](1_cilium.png)
  
- **Cluster components (scheduler, etcd, controller-manager)**  
  ![Component Status](1_components_status.png)
  
- **Cluster nodes**  
  ![Nodes List](1_get_nodes_wide.png)
  
- **Worker Node 1 details**  
  ![Worker Node 1](1_com_node_1.png)
  
- **Worker Node 2 details**  
  ![Worker Node 2](1_com_node_2.png)
  
- **Master Node details**  
  ![Master Node 1](1_com_mas_1.png)  
  ![Master Node 2](1_com_master_2.png)

---

## **4. Task 2 - Deploying a Client-Server Application and Testing**

### **4.1 Configuration Details**
Our client-server application consists of:
- A **server application** (Deployment type)
- A **client application** (Pod type)
- A **Service** ensuring communication between the client and server

### **4.2 Scenario 2a**
#### **Task Description:**
Expose the server application using a **NodePort** Service. Demonstrate communication with the server using its IP and the appropriate port.

#### **Implementation Steps:**
- **Summary of created resources:**  
  ![Created Services, Pods, and Nodes](2_10_A_utworzone_serwisy.png)

- **Locating the server pod for correct addressing:**  
  ![Server Application Location](2_3_A_IP.png)

- **Testing access to the server from different locations:**
  - **Inside the client application pod:**  
    ![Curl from Client Pod](2_2_A_pod.png)
  
  - **From the master node (inside the cluster):**  
    ![Curl from Node](2_1_A_node_port.png)
  
  - **Outside the cluster:**  
    A fourth virtual machine (`test`) was created in the same network as the cluster. The server was tested from this external machine:
    ![External Access Test](2_8_A_vms.png)
    ![Curl from External Machine](2_9_A_spoza_klastra.png)

### **4.3 Scenario 2b**
#### **Task Description:**
Expose the server application using a **ClusterIP** Service and test communication with the server using the **FQDN** `service-name.namespace.svc.cluster.local`.

- **Created ClusterIP Service:**  
  ![ClusterIP Service](2_5_B_services.png)

- **Testing access from different locations:**
  - **Inside the client application pod:**  
    ![Curl from Client Pod](2_6_B_curl.png)
  - **From a node (inside the cluster):**  
    ![Curl from Node](2_7_B_could_not_resolve.png)

  It was observed that using the recommended FQDN does not allow access from within the cluster due to missing DNS configuration.

### **4.4 Comparison of Both Scenarios**
- **Scenario 2a:** Uses **NodePort**, which enables access to the service from both inside and outside the cluster.
- **Scenario 2b:** Uses **ClusterIP**, which allows access **only inside** the cluster. The client application must be deployed within the cluster.

---

## **5. Task 3 - Observability with OpenTelemetry**
The task involved setting up monitoring tools (Grafana, Mimir, OpenTelemetry) and integrating them to collect cluster insights.

### **5.1 Installation of Tools + Cert Manager**
After installation, verification was done to ensure proper deployment:
- **Mimir Pods:**  
  ![Mimir Pods](3_2_mimir_pod.png)
- **Mimir Services:**  
  ![Mimir Services](3_3_mimir_svc.png)
- **Grafana Resources:**  
  ![Grafana Resources](3_4_grafana.png)
- **OpenTelemetry Resources:**  
  ![OpenTelemetry Resources](3_5_opentelemetry.png)
- **Certificate Manager Resources:**  
  ![Cert Manager Resources](3_6_cer.png)

### **5.2 Integration of Grafana with Mimir and OpenTelemetry**
Grafana was integrated with Mimir using the `mimir-nginx` service.

### **5.3 Main Objective**
Scenario **3b** was implemented:
- Deploy at least one **OpenTelemetry Collector** without using the OpenTelemetry Operator.
- The configuration included:
  - At least **one Receiver** for collecting telemetry data.
  - At least **one Processor** for adding labels (e.g., `group_number=<group_number>`).
  - At least **one Exporter** for sending telemetry data to the chosen backend.

---

## **6. Conclusion**
This project successfully deployed a Kubernetes cluster, set up observability tools, and tested service exposure mechanisms. Future work involves enhancing DNS configuration and expanding monitoring capabilities.

