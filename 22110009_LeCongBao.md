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
step 4: Start the containers interactively
```bash
docker start -i sender
docker start -i receiver
```
step 5: Inspect the containers
```bash
docker inspect sender | findstr "IPAddress"
docker inspect receiver | findstr "IPAddress"
```
- We will have IP of `sender` and `receiver`
- 
![Screenshot 2024-11-25 160256](https://github.com/user-attachments/assets/c964bef2-e761-4ac5-92bf-aa8629ad21e4)

## 2: Create a plain text file
step 1: Dowload openssl 
```bash
apt install openssl -y
```
step 2: create a plain text file in `sender`
```bash
echo "This is simple text file" > plaintext.txt
```
![Screenshot 2024-11-25 161349](https://github.com/user-attachments/assets/b69f3edb-cc78-46a0-b526-dd03e71f73d9)


step 3: Creat a hash file by openssl
```bash
openssl sha256 plaintext.txt > plaintext.txt.hash
```
![Screenshot 2024-11-25 161858](https://github.com/user-attachments/assets/f7952769-c223-47a6-81d9-079c826a697c)


## 3: Tranfer file between two computers using Netcat
step 1: Install Netcat
```bash
sudo apt install netcat
```
step 2: Set up `receiver` to listen for incoming files
```bash
nc -l -p 12345 > received_plaintext.txt
nc -l -p 12345 > received_plaintext.txt.hash
```
![Screenshot 2024-11-25 163439](https://github.com/user-attachments/assets/64d987ae-7c1d-4a5d-bb09-9e6be859dd9f)

- In `sender`
```bash
nc 172.17.0.2 12345 < plaintext.txt
nc 172.17.0.2 12345 < plaintext.txt.hash
```
## 4: Verify the received files
step 1: Generate hash of the received files
```bash
openssl sha256 plaintext.txt
```
![Screenshot 2024-11-25 170014](https://github.com/user-attachments/assets/3650b9b7-c324-41d3-a808-a6dc53bf1c67)

step 2: Open the Hash file
```bash
nano received_plaintext.txt.hash
```
![Screenshot 2024-11-25 170446](https://github.com/user-attachments/assets/24a7f9b9-6d65-4a06-bdf8-6dc0eba71ab2)


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

