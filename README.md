# AWS Secure Web Server Project

This project demonstrates how to deploy a secure, resilient EC2 web server using AWS Identity and Access Management (IAM), Elastic Block Store (EBS) persistent storage, and fundamental Linux administration techniques.

---

## What is AWS?
Amazon Web Services (AWS) is a comprehensive cloud computing platform provided by Amazon that delivers on-demand IT infrastructure and services via the internet. As one of the world’s leading cloud platforms, it enables organizations to deploy scalable computing, storage, databases, and analytics without the overhead of maintaining physical data centers.

---

## AWS Services Used

* **IAM (Identity and Access Management):** Used to securely manage access and permissions for AWS resources.
* **Amazon EC2 (Elastic Compute Cloud):** Provides scalable, virtual computing instances (servers) in the cloud.
* **Amazon EBS (Elastic Block Store):** Delivers high-performance, persistent block storage volumes designed for use with EC2 instances.
* **Security Groups:** Acts as a virtual firewall to control inbound and outbound traffic for EC2 instances.

---

## Core Concepts Explained

### 1. AWS IAM
This service helps you securely control access to AWS resources. It allows you to manage users, roles, and permissions to strictly define who or what can access your AWS environment. IAM is a **global service**, meaning it is not region-specific and is managed centrally.

### 2. Amazon EC2
Amazon EC2 provides virtual machines (instances) in the cloud. Instead of purchasing physical hardware, you can launch these virtual servers in minutes and easily scale them up or down depending on your traffic demands.

### 3. Amazon EBS
EBS is a cloud-based storage service that provides durable, high-performance block storage volumes. It functions exactly like a virtual hard drive attached to a physical server, ensuring data persists independently of the life of the EC2 instance.

### 4. Security Groups
Security Groups act as a stateful, host-level firewall for your EC2 instances, allowing you to set explicit rules to control both inbound and outbound network traffic.

---

## Architecture Diagram

<img width="1371" height="990" alt="AWS Architecture Diagram" src="https://github.com/user-attachments/assets/17f50ad2-dd96-413e-8809-678a6cfc529b" />

---

## Steps Performed

1.  **IAM Setup:** Created an IAM user with `AdministratorAccess` for project deployment, and created an IAM instance profile (role) with the `AmazonSSMManagedInstanceCore` policy attached.
2.  **EC2 Provisioning:** Launched an EC2 instance using a startup script (User Data) to automatically install and configure the Nginx web server. 
3.  **Security Group Configuration:** Adjusted the Security Group rules to allow specific inbound traffic (SSH and HTTP) while permitting all outbound traffic.
4.  **Role Assignment:** Attached the created IAM role to the EC2 instance to grant it permissions to interact securely with AWS systems.
5.  **Service Verification:** Accessed the EC2 instance and verified that the Nginx service was running successfully.
6.  **Storage Expansion:** Created a 10GB EBS volume in the same Availability Zone and attached it to the EC2 instance.
7.  **Linux Storage Management:** Formatted and mounted the new EBS volume to a local directory.
8.  **Nginx Reconfiguration:** Modified the default Nginx configuration file to point the web root directory to the newly mounted EBS volume location.
9.  **Web Hosting:** Created a custom `index.html` file in the mounted storage directory and verified web access using the instance's public IP address.
10. **Resilience Testing:** Unmounted and detached the EBS volume, then terminated the original EC2 instance to simulate a server failure.
11. **Data Recovery & Recovery:** Launched a new identical EC2 instance, attached the existing EBS volume, remounted it, and confirmed that the test website was fully restored and accessible via the new public IP.

---

## IAM Policies Utilized

* `AdministratorAccess` (Used for initial user environment creation)
* `AmazonSSMManagedInstanceCore` (Used to allow secure systems management and access)

---

## Linux Commands Reference

```bash
# Verify, enable, and start the Nginx web server
sudo systemctl status nginx
sudo systemctl enable nginx
sudo systemctl start nginx

# Create a deployment directory structure
sudo mkdir -p /data/website/html

# Storage administration: View storage devices, format, and mount the EBS volume
lsblk
sudo mkfs.ext4 /dev/xvdf
sudo mount /dev/xvdf /data
df -h

# Modify Nginx configuration (Change root directory)
sudo vi /etc/nginx/nginx.conf
sudo systemctl restart nginx

# Deploy web content
cd /data/website/html
vi index.html

# Safely unmount volume during migration/cleanup
sudo umount /data
```

## Hosted website output
<img width="1885" height="1014" alt="1st_instance_index_page" src="https://github.com/user-attachments/assets/14077813-65d1-4684-a46e-c746767d4731" />


# Conclusion
This project shows how separating your server (EC2) from your storage (EBS) keeps your data safe.
When the first web server was deleted, we did not lose any files because the website data was saved safely on the separate EBS volume. By connecting that same volume to a brand-new server, we proved how quickly a website can recover if a server breaks. Finally, using IAM roles and Security Groups keeps the whole setup safe from online threats.
