# Lab #2,22110009, Le Cong Bao, INSE330380E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 

**Answer 1**:
## 1. Create a text file named `plain.txt`:
*First, we write a message and save it in a text file:*<br>

```sh
echo "This is a sample file for testing integrity and authenticity." > plain.txt
```
## 2. Create a Bridge Network between 2 container
```bash
docker network create --driver bridge my_network
docker network ls
```
![Screenshot 2024-11-25 094926](https://github.com/user-attachments/assets/d7eaded2-6420-4618-a6a8-081e8251310b)

- `docker network ls` : kiểm tra xem mạng đã được tạo chưa
## 3. Ping 2 virtual computers container1 (sender) và container2 (receiver)
```bash
docker exec -it container1 bash
ping container2
```
## 4. In container1 (sender) create a hash
```bash
openssl enc -sha256 -in plain.txt -out plain.txt.sha256
```


Verify current folder for newly created file
 
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
