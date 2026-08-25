<div align="center">

# 🔐 Lab 001 — Basic Router Security Configuration

![Cisco](https://img.shields.io/badge/Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![Packet Tracer](https://img.shields.io/badge/Packet%20Tracer-0056A6?style=for-the-badge&logo=cisco&logoColor=white)
![Topic](https://img.shields.io/badge/Topic-Password%20Security-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

*Part of my [CCNA 200-301 Study & Lab Tracker](../) series*

</div>

---

## 🎯 Objective

This lab demonstrates why the classic `enable password` command is **not secure by default**, and how the `service password-encryption` command changes that — using two Cisco 1941 routers (**R1** and **R2**) connected directly via their `GigabitEthernet0/0` interfaces.

<br>

## 🖧 Topology

| Device | Model | Interface | Role |
|---|---|---|---|
| R1 | Cisco 1941 | Gig0/0 | Router 1 |
| R2 | Cisco 1941 | Gig0/0 | Router 2 |

<p align="center">
  <img src="screenshots/01-topology-tasklist.png" width="600" alt="Lab task list and initial topology"/>
</p>
<p align="center">
  <img src="screenshots/02-topology-connected.png" width="450" alt="R1 and R2 connected via Gig0/0"/>
</p>

<br>

## 📋 Lab Tasks

1. Connect R1 and R2 by their GigabitEthernet0/0 interfaces
2. Set the hostnames according to the network diagram (R1 and R2)
3. Set the enable password on each router to `cisco`
4. View the password in the running configuration — is it encrypted?
5. Enable password encryption on each router
6. View the password in the running configuration — is it encrypted?
7. Disable password encryption on each router
8. View the password in the running configuration — is it encrypted?

<br>

## ⚙️ Step-by-Step Configuration

### 1️⃣ Set Hostnames

```bash
Router#configure terminal
Router(config)#hostname R1
```
*(Repeated on R2 with hostname `R2`)*

<p align="center">
  <img src="screenshots/03-hostname-config.png" width="650" alt="Setting hostnames on R1 and R2"/>
</p>

### 2️⃣ Set Enable Password

```bash
R1(config)#enable password cisco
R1(config)#exit
```

<p align="center">
  <img src="screenshots/04-enable-password-r1.png" width="450" alt="Setting enable password on R1"/>
  <br/>
  <img src="screenshots/05-enable-login-r1.png" width="300" alt="Logging into privileged mode with the password"/>
</p>

### 3️⃣ Verify — Before Encryption

```bash
R1#show running-config
...
enable password cisco
...
```

<p align="center">
  <img src="screenshots/06-running-config-r1-plaintext.png" width="450" alt="R1 running-config showing plaintext password"/>
  <img src="screenshots/07-running-config-r2-plaintext.png" width="450" alt="R2 running-config showing plaintext password"/>
</p>

> 🔎 **Observation:** The password appears in **plain text**.
> **Is it encrypted? → ❌ NO**

### 4️⃣ Enable Password Encryption

```bash
R1(config)#service password-encryption
```
*(Repeated on R2)*

<p align="center">
  <img src="screenshots/08-service-encryption-r1.png" width="450" alt="Enabling service password-encryption on R1"/>
  <img src="screenshots/09-service-encryption-r2.png" width="450" alt="Enabling service password-encryption on R2"/>
</p>

### 5️⃣ Verify — After Encryption

```bash
R1#show running-config
...
enable password 7 0822455D0A16
...
```

<p align="center">
  <img src="screenshots/10-running-config-r1-encrypted.png" width="450" alt="R1 running-config showing Type 7 encrypted password"/>
  <img src="screenshots/11-running-config-r2-encrypted.png" width="450" alt="R2 running-config showing Type 7 encrypted password"/>
</p>

> 🔎 **Observation:** The password is now shown as a **Type 7 encoded value**.
> **Is it encrypted? → ✅ YES**

### 6️⃣ Disable Password Encryption

```bash
R1(config)#no service password-encryption
```
*(Repeated on R2)*

<p align="center">
  <img src="screenshots/12-no-service-encryption-r1.png" width="450" alt="Disabling service password-encryption on R1"/>
  <img src="screenshots/13-no-service-encryption-r2.png" width="450" alt="Disabling service password-encryption on R2"/>
</p>

### 7️⃣ Verify — After Disabling Encryption

```bash
R1#show running-config
...
enable password 7 0822455D0A16
...
```

<p align="center">
  <img src="screenshots/14-running-config-r1-still-encrypted.png" width="450" alt="R1 running-config still showing encrypted password"/>
  <img src="screenshots/15-running-config-r2-still-encrypted.png" width="450" alt="R2 running-config still showing encrypted password"/>
</p>

> 🔎 **Observation:** The password **stays encrypted** in the config even after the command is disabled.
> **Is it encrypted? → ✅ YES**

<br>

## 🧠 Key Takeaway

`service password-encryption` is a **one-way, retroactive-on-existing-passwords** setting:

- Turning it **ON** immediately obscures all plaintext passwords in the config using weak **Type 7** encoding.
- Turning it **OFF** only stops **future** passwords from being encrypted — it does **not** decrypt passwords that were already converted.
- ⚠️ Type 7 encryption is a simple XOR-based cipher and is **easily reversible** with widely available tools — it should never be relied on as real security. Use `enable secret` (MD5/SCRYPT-hashed) for genuine protection instead of `enable password`.

<br>

## 📊 Summary Table

| Step | Command | Password in Config | Encrypted? |
|---|---|---|---|
| 3 | `enable password cisco` | `enable password cisco` | ❌ No |
| 5 | `service password-encryption` | `enable password 7 0822455D0A16` | ✅ Yes |
| 7 | `no service password-encryption` | `enable password 7 0822455D0A16` | ✅ Yes *(unchanged)* |

<br>

## 🗂️ Files in This Lab

```text
📦 001-Basic-Router-Security-Configuration-1/
 ┣ 📄 001-Basic-Router-Security-Configuration-1.pkt   # Packet Tracer topology
 ┣ 📂 screenshots/                                     # Step-by-step CLI screenshots
 ┃ ┣ 🖼️ 01-topology-tasklist.png
 ┃ ┣ 🖼️ 02-topology-connected.png
 ┃ ┣ 🖼️ 03-hostname-config.png
 ┃ ┣ 🖼️ 04-enable-password-r1.png
 ┃ ┣ 🖼️ 05-enable-login-r1.png
 ┃ ┣ 🖼️ 06-running-config-r1-plaintext.png
 ┃ ┣ 🖼️ 07-running-config-r2-plaintext.png
 ┃ ┣ 🖼️ 08-service-encryption-r1.png
 ┃ ┣ 🖼️ 09-service-encryption-r2.png
 ┃ ┣ 🖼️ 10-running-config-r1-encrypted.png
 ┃ ┣ 🖼️ 11-running-config-r2-encrypted.png
 ┃ ┣ 🖼️ 12-no-service-encryption-r1.png
 ┃ ┣ 🖼️ 13-no-service-encryption-r2.png
 ┃ ┣ 🖼️ 14-running-config-r1-still-encrypted.png
 ┃ ┗ 🖼️ 15-running-config-r2-still-encrypted.png
 ┗ 📜 README.md                                        # This file
```

<br>

## 🔗 Related

- 🎥 Based on: [Jeremy's IT Lab — CCNA 200-301](https://youtube.com/playlist?list=PLxbwE86jKRgMQ4HTuaJ7yQgA2BoNwY9ct)
- 📁 Part of: [CCNA 200-301 Study & Packet Tracer Lab Tracker](../)

---

<div align="center">

🖇️ *Documented as part of hands-on CCNA 200-301 practice*

</div>
