---
name: Network Decommissioning
about: Decommission a network and remove related infrastructure
title: 'Network Decommission - {insert network name}'
labels: NETWORK-DECOMMISSION, NODE-MAINTENANCE
---

# **Network Decommissioning Checklist**
- [ ] Stop and purge **validator Nomad job**.
- [ ] Stop and purge **any full-nodes** (e.g., `sentry0`, `relayer0`).
- [ ] Stop and purge **TMKMS** associated with the validator.
- [ ] SSH into respective servers and **remove Docker volumes**.
- [ ] Remove **Consul configs**.
- [ ] Remove **Vault configs**.
    - *Ensure mnemonics are no longer needed and are backed up if so.*
- [ ] Remove **Zabbix hosts**.
- [ ] Remove **jobs from the infra repo**.

### **Completion Criteria**
##### ✅ _The network is considered **fully decommissioned** when:_
  - The **validator, full-nodes, and TMKMS** Nomad jobs are stopped and purged.
  - **Docker volumes** are removed from all relevant servers.
  - The network is deleted from **Consul** and **Vault**.
  - **Zabbix monitoring** is no longer active for the network.
  - The **infra repo** no longer contains job files for the network.

---

## **Network Decommissioning Guide**
To fully remove a network and its associated infrastructure, follow these steps:

### **Stop and Purge Nomad Jobs**
  1. **Login to Nomad** and search for the network you are decommissioning.
2. **Stop and purge the following jobs** in order:
  - **Validator Nomad job**.
  - **Full-nodes** (e.g., `sentry0`, `relayer0`).
  - **TMKMS** associated with the validator.

### **Remove Docker Volumes**
1. **SSH into each respective server**:
  ```
  ssh prod1
  ```
2. **Switch to root user**:
  ```
  sudo su -
  ```
3. **Navigate to the Docker volumes directory**:
  ```
  cd /var/lib/docker/volumes
  ```
4. **Remove Docker volumes**:
    ```
      docker rm volume <networkName-node>
      docker rm volume <tmkms-networkName>
      docker rm volume <networkName-prometheus-server>
      ```
      **Example**:
      ```sh
      docker rm volume diamond-1-validator
      ```

### **Remove Consul Configurations**
  1. **Login to Consul**.
  2. Navigate to the **Networks** tab.
  3. Find the network you are decommissioning.
  4. Click the **three dots** next to the network and select **Delete**.
  
### **Remove Vault Configurations**
  1. **Login to Vault**.
  2. Navigate to **Signing Keys**.
  3. Find the vault entry for the network.
  4. **Ensure mnemonics are no longer needed** and are backed up if required.
  5. Delete the vault entry.
  
### **Remove Zabbix Hosts**
  1. **Login to Zabbix**.
  2. Go to the **Hosts** section.
  3. Search for the host you are removing.
  4. Click on the host and select **Delete**.

### **Remove Jobs from the Infra Repository**
1. **Navigate to the infra repository in your terminal**:
  ```
  cd ~/infra
  git pull
  ```
2. **Create a new branch for decommissioning**:
  ```
  git checkout -b dp/decommission-<network>
  ```
  3. **Find the folder related to the network and delete it**.
4. **Commit your changes and push the branch**:
  ```
  git add .
  git commit -m "Decommission <network> and remove infra jobs"
  git push origin dp/decommission-<network>
  ```
  5. **Open a pull request** to finalize the changes.
