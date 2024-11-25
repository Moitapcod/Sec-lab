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

### Conclusion
i have tranfered the files between 2 computers using Netcat and verified the content of the files
# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1. Create a file 
```bash
echo "This is a secret message." > message.txt
```
## 2. Generate a 2048-bit RSA private key  used by the `receiver`
- Generate the private key
```bash
openssl genrsa -out private_key.pem 2048
```
![Screenshot 2024-11-25 210738](https://github.com/user-attachments/assets/5155f57e-41a6-4fd2-b88c-73857b9e9c93)
- Extract public key
```bash
openssl rsa -in private_key.pem -pubout -out public_key.pem
```
![Screenshot 2024-11-25 211300](https://github.com/user-attachments/assets/c8d273eb-0b37-4124-9fe0-608ae3549ddc)
## 3. The `receiver` shares the public_key.pem file with the `sender` using `Netcat`
-In `receiver`:
```bash
nc 172.17.0.2 12345 <  public_key.pem
```
-In `sender`:
```bash
nc -l -p 12345 >  public_key.pem
```
## 4. Create and Encrypt Symmetric Key on `sender`
- Create a symmetric AES key (32 bytes for AES-256):
```bash
oppenssl rand -base64 32 > symmetric_key.txt
```
![Screenshot 2024-11-25 220007](https://github.com/user-attachments/assets/4ee291a7-b14e-4b92-8497-bb1ccca9d888)

- Encrypt the symmetric key using Computer B’s public RSA key. The Sender encrypts the symmetric key using the Receiver's public RSA key. The encrypted symmetric key is saved to encrypted_key.bin.
```bash
openssl pkeyutl -encrypt -inkey public_key.pem -pubin -in symmetric_key.txt -out encrypted_key.bin
```
- Note:
+ `openssl pkeyutl`: This command is used for public key cryptography operations like encryption and decryption.
+ `-encrypt`: Specifies that the operation is encryption.
+ `-inkey public_key.pem`: Specifies the public key file (public_key.pem) used for encryption.
+ `-pubin`: Indicates that the input key is a public key (as opposed to a private key).
![Screenshot 2024-11-25 220845](https://github.com/user-attachments/assets/eba7ef1e-6690-4875-bc42-ac9f7e7491f9)
- Encrypt the file message.txt using the symmetric AES key. The Sender encrypts message.txt using AES-256-CBC with the symmetric key stored in symmetric_key.txt. The encrypted file is saved as encrypted_file.bin.
```bash
openssl enc -aes-256-cbc -salt -in message.txt -out encrypted_file.bin -pass file:symmetric_key.txt -pbkdf2
```
- Note:
+ `-aes-256-cbc`: Specifies the encryption algorithm (AES-256-CBC)
+ `-salt`: Adds a salt to the encryption process to prevent dictionary attacks and enhance security by ensuring the output differs even with identical inputs.
+ `-pbkdf2`: Uses PBKDF2 (Password-Based Key Derivation Function 2) to derive a secure key from the passphrase. This makes the encryption more secure by applying multiple iterations of a cryptographic hash function.
![Screenshot 2024-11-25 223023](https://github.com/user-attachments/assets/b38a4d7c-583c-4710-8a1a-72b5c2631e88)
## 5. The `sender` send both `encrypted_key.bin` and `encrypted_file.bin` to the `receiver`
- In `receiver`:
```bash
nc -l -p 12345 > encrypted_key.bin
nc -l -p 12345 > encrypted_file.bin
```
- The Receiver listens on port 1234 for both the encrypted symmetric key (encrypted_key.bin) and the encrypted file (encrypted_file.bin)
- In `sender`:
```bash
nc 172.17.0.3 12345 < encrypted_key.bin
nc 172.17.0.3 12345 < encrypted_file.bin
```
- The Sender sends both the encrypted_key.bin and encrypted_file.bin to the Receiver over netcat to the IP address 172.17.0.3 on port 12345
## 6. Decrypt Symmetric Key on `receiver`
```bash
openssl pkeyutl -decrypt -inkey private_key.pem -in encrypted_key.bin -out symmetric_key.txt
```
- The `receiver` uses their private RSA key to decrypt the `encrypted_key.bin file`, retrieving the symmetric key and saving it to `symmetric_key.txt`
- Note:
+ `openssl pkeyutl`: This command is used for public key cryptography operations, such as encryption, decryption, and signing.
+ `-decrypt`: This will decrypt the data that was previously encrypted using a public key.
+ `-inkey private_key.pem`: Specifies the private key `private_key.pem` used to decrypt the data.
+ `-in encrypted_key.bin`: The input file `encrypted_key.bin` contains the encrypted data
+ `-out symmetric_key.txt`: Specifies the output file `symmetric_key.txt` where the decrypted data (the original symmetric key) will be saved.
![Screenshot 2024-11-25 230737](https://github.com/user-attachments/assets/ef0f7775-a308-4527-9684-19934da3cde6)
## 7. Decrypt the File on `reciver`
```bash
openssl enc -d -aes-256-cbc -in encrypted_file.bin -out decrypted_example.txt -pass file:symmetric_key.txt -pbkdf2
```
- Note:
+ The `receiver` decrypts the encrypted file encrypted_file.bin using the symmetric key stored in symmetric_key.txt and saves the decrypted content to decrypted_example.txt.
+ `openssl enc`: This command is used for symmetric encryption and decryption operations using OpenSSL, where the same key is used for both encryption and decryption.
+ `-d`: Specifies that the operation is decryption. Without this option, the command would default to encryption.
+ `-aes-256-cbc`: Specifies the encryption algorithm and mode
+ `-pass file:symmetric_key.txt`: Specifies the password or passphrase for decryption, which is read from the file `symmetric_key.txt`.
+ `-pbkdf2`: Uses PBKDF2 (Password-Based Key Derivation Function 2) to securely derive the encryption key from the passphrase.
  
```bash
nano decrypted_example.txt
```
![Screenshot 2024-11-25 231432](https://github.com/user-attachments/assets/f2c86ac3-315f-489e-b855-358562401e62)

## Conclusion
- in this task, i transfered an encrypted file between two computers using hybrid encryption
- RSA Keys: The receiver generates an RSA key pair. The public key is shared with the sender to encrypt the symmetric key.
- Symmetric Key Encryption: The sender generates a symmetric AES key, encrypts it with the receiver's public RSA key, and encrypts the file using AES-256.
- The `receiver` successfully decrypts both the symmetric key and the file, retrieving the original message "This is a secret message."

# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests on the other host.

