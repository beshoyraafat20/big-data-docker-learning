# 🚀 Big Data & Docker Hands-on Learning Path (Pseudo-Distributed Mode)

Welcome to the **`big-data-docker-learning`** repository! This project serves as a practical, step-by-step technical guide for establishing a pseudo-distributed Big Data environment using **Docker**, **Java 11**, **Apache Hadoop (HDFS)**, and **Apache HBase** running under a dedicated non-root Linux user (`hduser`) on an **Ubuntu 22.04** container.

---

## 📌 Prerequisites
* A **GitHub** account with **GitHub Codespaces** enabled (or local Docker Desktop).
* Basic Linux terminal / CLI experience.

---

## ☕ Phase 2: System Setup & System-Wide Dependency Installation

Inside your `bigdata-node` container terminal (`root@...:/#`):

### 1. Update Package Index & Install Core System Packages
Install Java 11 (system-wide), SSH, Sudo, nano, and essential utilities:
```bash
apt-get update && apt-get install -y openjdk-11-jdk wget tar nano openssh-server openssh-client sudo
```

### 2. Verify System Java Installation
```bash
java -version
```
*Verification:* Confirm OpenJDK 11 output is displayed. Location is registered system-wide at `/usr/lib/jvm/java-11-openjdk-amd64`.

---

## 👤 Phase 3: Dedicated Hadoop User & Permission Setup

To adhere to security best practices, run Big Data daemons under a dedicated user (`hduser`) instead of `root`.

### 1. Create User Group and Hadoop User
Create the `hadoop` group and user using `adduser`:
```bash
adduser --ingroup hadoop hduser
```
*(Follow the interactive prompts to set a password for `hduser`).*

### 2. Grant Sudo Privileges & Passwordless Config

* **Grant sudo group access:**
 Open the sudoers file securely:
  ```bash
  sudo visudo
  ```
  Add the following line at the very bottom of the file:
  ```text
  hduser ALL=(ALL:ALL) ALL
  ```
  *(Save and exit the editor: `Ctrl + O`, `Enter`, then `Ctrl + X`).*

### 3. Switch to `hduser`
```bash
su - hduser
```
*Verification:* Your terminal prompt will change to `hduser@...:~$`.

---

## 🐘 Phase 4: Apache Hadoop (HDFS) Setup & Trusted Key SSH

In this phase, you download Hadoop, configure environment variables, establish Trusted SSH Key authentication, and format HDFS.

### 1. Download & Extract Hadoop 3.3.6
```bash
sudo wget https://archive.apache.org/dist/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz -P /opt/
sudo tar -xzvf /opt/hadoop-3.3.6.tar.gz -C /opt/
sudo mv /opt/hadoop-3.3.6 /opt/hadoop
sudo chown -R hduser:hadoop /opt/hadoop
sudo chmod -R 777 /opt/hadoop
```

### 2. Configure Environment Variables for `hduser`
Add required paths to the user's bash profile:
```bash
echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64" >> ~/.bashrc
echo "export HADOOP_HOME=/opt/hadoop" >> ~/.bashrc
echo "export PATH=\$PATH:\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin" >> ~/.bashrc
source ~/.bashrc
```

### 3. Setup Trusted Key SSH Authentication (Passwordless Login)
Hadoop daemons require passwordless SSH access to `localhost`:
```bash
sudo service ssh start
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
*Verification:* Test the trusted key connection:
```bash
ssh localhost
```
*(Type `yes` when prompted for fingerprint confirmation; it should log in without asking for a password).*

### 4. Configure Hadoop XML Files

* **Configure `core-site.xml`:**
  ```bash
  nano $HADOOP_HOME/etc/hadoop/core-site.xml
  ```
  Add inside `<configuration>`:
  ```xml
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://localhost:9000</value>
      </property>
  </configuration>
  ```

* **Configure `hdfs-site.xml`:**
  ```bash
  nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
  ```
  Add inside `<configuration>`:
  ```xml
  <configuration>
      <property>
          <name>dfs.replication</name>
          <value>1</value>
      </property>
  </configuration>
  ```

* **Set `JAVA_HOME` in Hadoop Configuration:**
  ```bash
  echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64" >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
  ```

### 5. Format NameNode & Start HDFS Services
```bash
hdfs namenode -format
start-dfs.sh
```
*Verification:* Execute `jps` and confirm **`NameNode`**, **`DataNode`**, and **`SecondaryNameNode`** processes are active.

---

## 🗄️ Phase 5: Apache HBase Deployment (Pseudo-Distributed Mode over HDFS)

In this phase, you bind HBase to HDFS storage.

### 1. Download & Extract HBase 2.5.8
```bash
sudo wget https://archive.apache.org/dist/hbase/2.5.8/hbase-2.5.8-bin.tar.gz -P /opt/
sudo tar -xzvf /opt/hbase-2.5.8-bin.tar.gz -C /opt/
sudo mv /opt/hbase-2.5.8 /opt/hbase
sudo chown -R hduser:hadoop /opt/hbase
sudo chmod -R 777 /opt/hbase
```

### 2. Configure Java Environment for HBase
```bash
echo "export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64" >> /opt/hbase/conf/hbase-env.sh
```

### 3. Configure `hbase-site.xml` for HDFS Integration
```bash
nano /opt/hbase/conf/hbase-site.xml
```
Add inside `<configuration>`:
```xml
<configuration>
    <property>
        <name>hbase.rootdir</name>
        <value>hdfs://localhost:9000/hbase</value>
    </property>
    <property>
        <name>hbase.cluster.distributed</name>
        <value>true</value>
    </property>
    <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/tmp/zookeeper</value>
    </property>
</configuration>
```

### 4. Add HBase Executables to User Path
```bash
echo "export HBASE_HOME=/opt/hbase" >> ~/.bashrc
echo "export PATH=\$PATH:\$HBASE_HOME/bin" >> ~/.bashrc
source ~/.bashrc
```

### 5. Start Apache HBase Services
```bash
start-hbase.sh
```

### 6. Verify Active Daemons
```bash
jps
```
*Verification:* Output should confirm `NameNode`, `DataNode`, `SecondaryNameNode`, `HMaster`, and `HRegionServer`.

---

## 💻 Phase 6: Hands-on HBase Shell & NoSQL Operations

### 1. Launch Interactive Shell
```bash
hbase shell
```

### 2. Execute CRUD Operations
```hbase
# Create table 'users' with column family 'cf'
create 'users', 'cf'

# Insert a record (Row Key: row1, Column: cf:name, Value: Beshoy)
put 'users', 'row1', 'cf:name', 'Beshoy'

# Scan table contents persisted to HDFS
scan 'users'

# Exit interactive shell
exit
```

---

## 📝 Roadmap & Progress Checklist
- [x] **Phase 1:** Docker Container Infrastructure (`bigdata-node`)
- [x] **Phase 2:** System-wide Java 11 & Essential Utilities Setup
- [x] **Phase 3:** Dedicated System User (`hduser`), Sudo Group & Visudo Setup
- [x] **Phase 4:** Apache Hadoop HDFS Configuration, Trusted Key SSH & Startup
- [x] **Phase 5:** Apache HBase Pseudo-Distributed Deployment (`HMaster` & `HRegionServer`)
- [x] **Phase 6:** NoSQL Data Operations over HDFS (`create`, `put`, `scan`)
- [ ] **Phase 7:** Multi-Container Cluster Orchestration using Docker Compose