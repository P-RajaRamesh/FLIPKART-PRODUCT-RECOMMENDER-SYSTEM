# 🛍️ FLIPKART PRODUCT RECOMMENDER
The Flipkart Product Recommender System is an intelligent application designed to list top products based on user queries, from Flipkart's product review dataset. 
It supports contextual chat history, enabling responses that consider previous interactions for a more personalized experience.
It leverages the power of Large Language Models (LLMs) through the GROQ inference API, orchestrated using the LangChain framework.

## 🔧 Core Workflow
- **🗣️ User Query:** Accepts natural language input describing product preferences.
- **🧠 LLM Processing:** GROQ-powered LLM interprets the query and extracts top products via the LangChain framework.
- **📊 Dataset Matching:** Flipkart product reviews from an Excel dataset is used as the source for suggestions.
- **🔍 Semantic Embedding:** The dataset is transformed into vector embeddings using Hugging Face.
- **🗃️ Vector Search:** Embeddings are stored in an online vector database powered by Astra DB.
- **🖥️ Frontend Interface:** A Bootstrap HTML app provides an interactive chatbot UI for users to input queries and get responses.
- **🐳 Deployment:** The entire application is containerized with Docker and deployed on a Kubernetes cluster running in a Google Cloud Platform (GCP) VM instance.
- **📈 Monitoring & Observability:** Integrated with Prometheus for metrics collection and Grafana for real-time visualization and dashboarding.

## 📦 Installating & Running Locally
- **Clone this repo & CD FLIPKART-PRODUCT-RECOMMENDER-SYSTEM :** ```git clone https://github.com/P-RajaRamesh/FLIPKART-PRODUCT-RECOMMENDER-SYSTEM.git```
- **Create Virtual Environment :** ```conda create -p venv1 python==3.10```
- **Activate Virtual Environment :** ```conda activate venv1\```
- **Install Requirements :** ```pip install -e .```
- **Create ```.env``` file:**
  ```
  GROQ_API_KEY="<YOUR-GROQ-API-KEY>"
  HF_TOKEN="<YOUR-HF-TOKEN>"
  HUGGINGFACEHUB_API_TOKEN="<YOUR-HUGGINGFACEHUB-API-TOKEN>"
  ASTRA_DB_API_ENDPOINT="<YOUR-ASTRA-DB-API-ENDPOINT>"
  ASTRA_DB_APPLICATION_TOKEN="<YOUR-ASTRA-DB-APPLICATION-TOKEN>"
  ASTRA_DB_KEYSPACE="default_keyspace"
  ```
- **Only once** Inject Flipkart Review dataset into Astra DB (uncomment the main function in data_ingestion.py) : ```flipkart/data_ingestion.py```
- Finally Run Flask app: ```python app.py```

## 💻 Pushing code to your Github
```
git init
git add .
git commit -m "commit"
git branch -m main
git remote add origin https://github.com/P-RajaRamesh/FLIPKART-PRODUCT-RECOMMENDER-SYSTEM.git
git push origin main
```

## ☁️ Deploying in Google CLoud VM:
### 1. Goto VM Instances and click **"Create Instance"**
  - Name: `Flipkart-Product-Recommender-System`
  - Machine Type:
    - Series: `E2`
    - Preset: `Standard`
    - Memory: `16 GB RAM`
  - Boot Disk:
    - Change size to `150 GB`
    - Image: Select **Ubuntu 24.04 LTS**
  - Networking:
    - Enable HTTP and HTTPS traffic
  - **Create the Instance**
---

### 2. Connect to the VM
  - Use the **SSH** option provided to connect to the VM from the browser.
---

### 3. Install Docker
  - Search: "Install Docker on Ubuntu"
  - Open the first official Docker website (docs.docker.com) - https://docs.docker.com/engine/install/ubuntu/
  - Scroll down and copy the **first big command block** and paste into your VM terminal : 1. Set up Docker's apt repository.
     ```
      # Add Docker's official GPG key:
      sudo apt-get update
      sudo apt-get install ca-certificates curl
      sudo install -m 0755 -d /etc/apt/keyrings
      sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      sudo chmod a+r /etc/apt/keyrings/docker.asc
      
      # Add the repository to Apt sources:
      echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
     ```
  - Then copy and paste the **second command block** : 2. Install the Docker packages.
    ```
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
  - Then run the **third command** to test Docker: 3. You will get error, you need to use sudo before it

    ```bash
    docker run hello-world
    ```

  - **Run Docker without sudo**
    - On the same page, scroll to: **"Post-installation steps for Linux"** : https://docs.docker.com/engine/install/linux-postinstall/
    - Paste all 4 commands one by one to allow Docker without `sudo`
      1. Create the docker group: ```sudo groupadd docker```      
      2. Add your user to the docker group: ```sudo usermod -aG docker $USER``` 
         - Log out and log back in so that your group membership is re-evaluated.
      If you're running Linux in a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.
      3. You can also run the following command to activate the changes to groups: ```newgrp docker```    
      4. Verify that you can run docker commands without sudo: ```docker run hello-world```

  - **Enable Docker to start on boot**
    - On the same page, scroll down to: **"Configure Docker to start on boot"**
    - Copy and paste the command block (2 commands one-by-one):
  
      ```bash
      sudo systemctl enable docker.service
      sudo systemctl enable containerd.service
      ```

  - **Verify Docker Setup** : ```docker version```
---

### 4. Install Minikube
  - Open browser and search `Install Minikube` 
  - Open the first official site (minikube.sigs.k8s.io) with `minikube start` on it: : https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download
  - Choose:
    - **OS:** Linux
    - **Architecture:** *x86*
    - Select **Binary download**
    - To install the latest minikube stable release on x86-64 Linux using binary download:
      1. Copy and run the below commands one-by-one
      ```
      curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
      sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
      ```
      2. Start your cluster: ```minikube start```
      - This uses Docker internally, which is why Docker was installed first
---

### 5. Install kubectl
  - Search: `Install kubectl on Ubuntu` : https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
  - Run the first command with `curl` from the official Kubernetes docs: 1. Download the latest release with the command:
    ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```
  - Go to the **Snap section** (below on the same page) : ```sudo snap install kubectl --classic```
  - Verify installation: ```kubectl version --client```
---

### 6. Interlink your Github to VM
  - Before cloning update local code by copying VM External IP from Google Cloud and past it in `prometheus\prometheus-configmap.yaml` file under flask-app targets by appending port `:5000`
  - Now push the code to Github :
      ```
      git add .
      git commit -m "commit"
      git push origin main
      ```
  - **Clone this repo & CD FLIPKART-PRODUCT-RECOMMENDER-SYSTEM :**
    ```
    git clone https://github.com/P-RajaRamesh/FLIPKART-PRODUCT-RECOMMENDER-SYSTEM.git
    cd FLIPKART-PRODUCT-RECOMMENDER-SYSTEM
    git config --global user.email "rajaramesh7410@gmail.com"
    git config --global user.name "P-RajaRamesh"
    
    git add .
    git commit -m "commit"
    git push origin main
    ```
  - Afetr this it will prompt for :
    - **Username**: `P-RajaRamesh`
    - **Password**: GitHub token (paste, it's invisible)
        - Goto Github Settings -> Developer Settings -> Personal Access Token -> Generate new token (classic)
        - Give the access permissions for the token: admin:org, admin:org_hook, admin:repo_hook, repo, workflow
---

### 7. Create a firewall rule in Google Cloud Console
  - Search for firewall
  - Create a firewall rule
  - Name the firewall : `flipkart-product-recommender-firewall`
  - Direction of traffic : Ingress
  - Action on match : Allow
  - Targets : All instances in the network
  - Source filter : IPv4 ranges
  - Source IPv4 ranges : 0.0.0.0/0
  - Protocols and ports : Allow All
---
    
### 8. Build and Deploy your APP on VM
  - Point Docker to Minikube instead locally on VM : ```eval $(minikube docker-env)```
  - Build the Docker image : ```docker build -t flask-app:latest .```
  - Verify docker image: ```docker images```
  - Configure environment secrets :
    ```
    kubectl create secret generic llmops-secrets \                                                                                        
      --from-literal=GROQ_API_KEY="<YOUR-GROQ-API-KEY>" \
      --from-literal=ASTRA_DB_APPLICATION_TOKEN="<ASTRA-DB-APPLICATION-TOKEN>" \
      --from-literal=ASTRA_DB_KEYSPACE="default_keyspace" \
      --from-literal=ASTRA_DB_API_ENDPOINT="<ASTRA-DB-API-ENDPOINT>" \
      --from-literal=HF_TOKEN="<YOUR-HF-TOKEN>" \
      --from-literal=HUGGINGFACEHUB_API_TOKEN="<YOUR-HF-TOKEN>"
    ```
  - Verify secrets : ```kubectl get secret llmops-secrets```
  - **Ignore** If wrong secrets entered : ```kubectl delete secret llmops-secrets```
  - **Ignore** If secrets are in another name space :```kubectl delete secret llmops-secrets -n my-namespace```
  - **Ignore** To get name spaces : ```kubectl get ns```
  - Apply Kubernetes deployment file to your cluster : ```kubectl apply -f flask-deployment.yaml```
  - To see PODs running : ```kubectl get pods```
  - In this terminal (SSH), port-forward for quickly accessing application externally :
    ```
    kubectl port-forward svc/flask-service 5000:80 --address 0.0.0.0
    ```
---

### 9. Now copy VM External IP in Google Cloud & append ```:8501``` and checkout your Application...
---

### 10. PROMETHEUS AND GRAFANA MONITORING OF YOUR APP
  - **Prometheus** configuration and deployment file applying to Kubernetes cluster
      - Open another SSH terminal and enter : ```cd FLIPKART-PRODUCT-RECOMMENDER-SYSTEM```
      - Create another namespace for monitoring (previously using default namespace) : ```kubectl create namespace monitoring```
      - Verify namespace creation : ```kubectl get ns```
      - Apply Kubernetes prometheus configuration file to your cluster :
        ```
        kubectl apply -f prometheus/prometheus-configmap.yaml
        ```
      - Apply Kubernetes prometheus deployment file to your cluster :
        ```
        kubectl apply -f prometheus/prometheus-deployment.yaml
        ```
      - To see prometheus running under monitoring namespace : ```kubectl get pods -n monitoring```

  - **Grafana** deployment file applying to Kubernetes cluster
      - Apply Kubernetes grafana deployment file to your cluster :
        ```
        kubectl apply -f grafana/grafana-deployment.yaml
        ```
      - To see grafana running under monitoring namespace : ```kubectl get pods -n monitoring```
        
  - In this terminal (SSH), port-forward for accessing prometheus metrics :
    ```
    kubectl port-forward --address 0.0.0.0 svc/prometheus-service -n monitoring 9090:9090
    ```
  - Now copy VM External IP in Google Cloud & append ```:9090``` and checkout prometheus metrics...
    
  - In another terminal (SSH), enter : ```cd FLIPKART-PRODUCT-RECOMMENDER-SYSTEM```
      - port-forward for accessing grafana dashboards :
        ```
        kubectl port-forward --address 0.0.0.0 svc/grafana-service -n monitoring 3000:3000
        ```

  - **Configure Grafana** - Username:Password --> admin:admin
     - Now copy VM External IP in Google Cloud & append ```:3000``` and checkout grafana dashboards...
     - Go to Settings -> Data Sources -> Add Data Source ->
     - Choose Prometheus -> Enter URL: http://prometheus-service.monitoring.svc.cluster.local:9090
     - Click Save & Test (Green success mesaage shown...)
     - Now create various visualization & dashboards.
