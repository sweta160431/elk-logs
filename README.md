# 🚀 ELK Stack: Centralized Logging with Docker

## 📌 Overview
This project sets up a centralized logging system using the **ELK Stack** (**Elasticsearch, Logstash, Kibana**) with **Filebeat** for log forwarding. The stack is containerized using **Docker Compose**.

## ✅ Tech Stack
- **Elasticsearch** - Stores and indexes logs
- **Logstash** - Processes and forwards logs
- **Kibana** - Visualizes logs in dashboards
- **Filebeat** - Ships log data from applications
- **Docker & Docker Compose** - Containerized deployment

## 🔹 Installation Steps
### **1️⃣ Install Docker & Docker Compose**
Ensure you have Docker and Docker Compose installed:
```sh
sudo apt update && sudo apt install -y docker.io
sudo apt install -y docker-compose
```
Verify installation:
```sh
docker --version
docker-compose --version
```

### **2️⃣ Install ELK Stack Using Docker Compose**
#### **Create a Project Directory**
```sh
mkdir elk-stack && cd elk-stack
```

#### **Create a `docker-compose.yml` File**
Define your ELK services inside this file.

#### **Create a Logstash Configuration File (`logstash.conf`)**
```sh
vim logstash.conf
```
Add the following Logstash pipeline configuration inside `logstash.conf`:
```yaml
input {
  beats {
    port => 5044
  }
}

filter {
  json {
    source => "message"
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "app-logs-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```

#### **Start ELK Stack Using Docker Compose**
```sh
docker-compose up -d
```

Check running containers:
```sh
docker ps
```

### **3️⃣ Configure Filebeat to Collect Logs**
#### **Install Filebeat**
```sh
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.17.9-linux-x86_64.tar.gz
tar -xzf filebeat-7.17.9-linux-x86_64.tar.gz
cd filebeat-7.17.9-linux-x86_64
```

#### **Edit Filebeat Configuration (`filebeat.yml`)**
```yaml
filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/*.log  # Change this to your application log path

output.logstash:
  hosts: ["localhost:5044"]
```

#### **Start Filebeat**
```sh
./filebeat -e
```
![Screenshot (38)](https://github.com/user-attachments/assets/45b2c30b-b64e-45d2-b330-84d4bde3ed21)


### **4️⃣ Verify Data in Elasticsearch**
Run:
```sh
curl -X GET "http://localhost:9200/_cat/indices?v"
```
You should see an index like app-logs-2025.02.06.

![Screenshot (39)](https://github.com/user-attachments/assets/b5c184c2-d43d-463f-8ccd-84e32965a539)


### **5️⃣ Visualize Logs in Kibana**
Open Kibana in your browser:
```
http://localhost:5601
```
![Screenshot (40)](https://github.com/user-attachments/assets/27168b77-84ee-4cb6-9791-1618bf24fdca)


Go to **Stack Management** → **Index Patterns**.
Click **Create Index Pattern** and enter `app-logs-*`.
Choose `@timestamp` as the time field.
Save and go to **Discover** to view logs.

![Screenshot (41)](https://github.com/user-attachments/assets/abb7e760-eb80-45e9-a648-6dc15c60f4d7)

## ✅ Outcome
A centralized logging system where:
- Logs from your application are collected by Filebeat.
- Logstash processes and forwards them to Elasticsearch.
- Kibana helps visualize the logs.

