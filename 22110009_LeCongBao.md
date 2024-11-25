# Lab #2,22110009, Le Cong Bao, INSE330380E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
## 1. Use Docker to pull Ubuntu and create 2 computers
step 1: pull Ubutu
```bash
docker pull ubuntu
```
step 2: Create 2 computers from Ubuntu
```bash
docker run --name sender -it ubuntu
docker run --name receiver -it ubuntu
```
step 3: Check list of containers running 
```bash
docker ps
```
![Screenshot 2024-11-25 153154](https://github.com/user-attachments/assets/a8e7ec85-8be2-456c-9605-3c0e94503008)

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1. Create 2 Docker containers representing two computers (VM1 and VM2).
```bash
docker run -it --name docker-machine-1 ubuntu bash
docker run -it --name docker-machine-2 ubuntu bash
```

# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests on the other host.

