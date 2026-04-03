# VirusTotal Investigation

## Objective

Conduct comprehensive investigation of malicious file hashes in VirusTotal and presenting the findings.

### Skills Learned

- Hash algorithm proficiency.
- Metadata analysis.
- Investigation and data filtering through analytical thinking.
- Validation of false positives and true positives.
- Sandbox report interpretation.
- Network forensics.

### Tools Used

- VirusTotal.
- Linux terminal and Powershell to create a file hash

## Steps

#### OpenVPN Installation

1. Make sure that system in updated by running the following command.

<img width="439" height="120" alt="image" src="https://github.com/user-attachments/assets/015b9f2b-c507-434b-835e-1588b3bbbdad" />

2. Next step is to install OpenVPN and easy-rsa software

<img width="437" height="129" alt="image" src="https://github.com/user-attachments/assets/6675d176-4095-4e72-a7dc-cef8c8ea4933" />

3. The following commands will create a folder in your home directory, copy the content /usr/share/easy-rsa folder to the created folder and we wil switch to the new directory.

<img width="442" height="217" alt="image" src="https://github.com/user-attachments/assets/639d2feb-46d3-446b-b52a-de6660e0df55" />

#### Building CA

1. We will now intiate PKI evironment by running the command below, which will allow us to create CA.

<img width="701" height="409" alt="image" src="https://github.com/user-attachments/assets/2f3ab2a6-ed49-43e0-8669-283bcf99baf3" />

2. We will now build the CA, set a passphrase and assign a hostname to the CA.

<img width="1022" height="721" alt="image" src="https://github.com/user-attachments/assets/25e5c79c-0beb-4703-bec7-180ccbc6dd8f" />

#### Generating certificates

1. Next we will generate server certificate and the key.

<img width="1804" height="539" alt="image" src="https://github.com/user-attachments/assets/142732fd-3510-4fa4-a8ca-2228f7fdc6d7" />

2. Now we will sign the server certificate with the CA. Type 'yes' under confimr request details and next enter the passphrase.

<img width="728" height="681" alt="image" src="https://github.com/user-attachments/assets/2b065bdf-897e-4c2e-87e0-6fb834bc8b54" />

3. Next we will generate Diffie-Hellman parameters

<img width="1349" height="492" alt="image" src="https://github.com/user-attachments/assets/886d5e96-bb55-4b52-83b4-3c3b17fbc307" />

4. It is now time to generate HMAC signatures for TLS authentication.

<img width="442" height="95" alt="image" src="https://github.com/user-attachments/assets/0f495a0e-2e22-47f6-84d1-9f8065b722b8" />

#### Configuring Server

1. First we will create server configuration file. In order to that we will create a new directory called /etc/openvpn and change to that directory

<img width="528" height="240" alt="image" src="https://github.com/user-attachments/assets/a60b7121-1b5f-44ea-b3b1-5e6272030a0f" />

2. Now we will type the below code into the config file.

```
port 1194
proto udp
dev tun
ca /etc/openvpn/ca.crt 
cert /etc/openvpn/server.crt
key /etc/openvpn/server.key
dh /etc/openvpn/dh.pem
auth SHA256
tls-auth /etc/openvpn/ta.key 0
server 10.8.0.0 255.255.255.0
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 8.8.8.8"
keepalive 10 120
cipher AES-256-CBC
persist-key
persist-tun
status /var/log/openvpn-status.log
verb 3
```
3. Now we will run commands to copy all the certificates we have created to the correct directory.

<img width="588" height="314" alt="image" src="https://github.com/user-attachments/assets/444386e0-2980-4f63-b7de-4a57f1e3ea14" />

#### Configuring network settings for the VPN.

1. First we will enable IP forwarding in order to receive correct IP address when connected to VPN server. In order to do that we have to update /etc/sysctl.conf configuration file.

```
sudo nano /etc/sysctl.conf
```
2. While viewing the file scroll down to line "#net.ipv4.ip_forward=1" and remove the # from the begining of the line to enable the IP forwarding. Press Ctrl X, type "Y" and press Enter to save the changes.

<img width="906" height="596" alt="image" src="https://github.com/user-attachments/assets/ea60831e-8162-4aea-8065-517ca10cd1c3" />

3. To apply the changes run the following command.

<img width="396" height="130" alt="image" src="https://github.com/user-attachments/assets/7727aeb2-da9f-4e05-9e74-949ba5a6cd7e" />
 
#### Configuring IP tables for NAT - Network Address Translation.

1. Run the following command.

<img width="639" height="102" alt="image" src="https://github.com/user-attachments/assets/fb19f022-b1dd-41e0-8f6b-39295b0a553d" />

2. Next run commands to make sure that IP tables have been updated with the rules we have just added.

