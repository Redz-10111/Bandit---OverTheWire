
![Bandit Image](../../Imagenes/level-0-1.png)


# Always set when connecting to a machine via SSH: `export TERM=xterm`

---

## 📄 Level description

The goal of this level is to connect to the game using **SSH**.  
Connection details are:

- ## **Server (host)**: `bandit.labs.overthewire.org`
    
- ## **Port**: `2220`
    
- ## **User**: `bandit0`
    
- ## **Password**: `bandit0`
    

Once logged in, you should move to the **Level 1** page to continue.

---

## 🔎 Objective of the level

Learn how to connect securely to a remote server with **SSH**, using a username, password, and a specific port.

---

## 🪜 Step by step (real console)

### 1. Verify connectivity with `ping`

![Bandit Image](../../Imagenes/level-0-3.png)

# {Command}

## `ping -c 1 bandit.labs.overthewire.org`



# {Command breakdown}

- **Binary**: `ping` → Sends ICMP packets to check if a host responds.
    
- **Option**: `-c 1` → Sends only 1 packet.
    
- **Parameter**: `bandit.labs.overthewire.org` → Target server address to check connectivity.
    

## 💬 {Teacher’s note}

Using `ping` is like ringing the doorbell of a house: if it answers, you know someone is inside.

---

### 2. Connect to the server with `ssh`

![Bandit Image](../../Imagenes/level-0-4.png)
![[level-0-4.png]]
# {Command}

## `ssh bandit0@bandit.labs.overthewire.org -p 2220`

# {Output}

## `The authenticity of host '[bandit.labs.overthewire.org]:2220' can't be established. ED25519 key fingerprint is SHA256:...
## `Are you sure you want to continue connecting (yes/no/[fingerprint])? yes`

# {Command breakdown}

- **Binary**: `ssh` → Secure connection client for servers.
    
- **User@host**: `bandit0@bandit.labs.overthewire.org` → User and target server.
    
- **Option**: `-p 2220` → Port used for the connection (default is 22).
    

# 💬 {Teacher’s note}

The `-p` parameter is like specifying which door to use: if you don’t provide it, it will always try the main door (22).

---

## 🧰 Most used `ssh` options

- **Basic usage**:  
    `ssh user@host`  
    Connects to port 22 by default.
    
- **`-p PORT`** → Connect on a different port.  
    Example: `ssh user@host -p 2200`
    
- **`-i key.pem`** → Authenticate with a private key.  
    Example: `ssh -i id_rsa user@host`
    
- **`-v`** → Verbose/debug mode.  
    Example: `ssh -v user@host`
    
- **`-L local:remote`** → Create SSH tunnels (port forwarding).  
    Example: `ssh -L 8080:localhost:80 user@host`
    

## 💬 {Teacher’s note}

SSH is like a secret passage: you can enter yourself, give out different keys, or even open tunnels to transfer hidden data.

---

## ❌ Common errors and solutions

- ❌ Forgetting to specify `-p 2220` → it will try port 22 and fail.
    
- ❌ Typo in the username (`bandit0` ≠ `bandito`).
    
- ❌ Not accepting the server fingerprint → prevents it from being saved in `known_hosts`.
    

---

## 🧾 Final cheat sheet

|Command|Purpose|Minimum usage|
|---|---|---|
|`ping -c 1 host`|Verify connectivity|`ping -c 1 bandit.labs.overthewire.org`|
|`ssh user@host -p port`|Connect via SSH|`ssh bandit0@bandit.labs.overthewire.org -p 2220`|

---

## 🧩 Final complete script

`#!/usr/bin/env bash set -euo pipefail  
`# Check server connectivity ping -c 1 bandit.labs.overthewire.org 
`# Connect to the server using SSH on port 2220 ssh bandit0@bandit.labs.overthewire.org -p 2220`

---

## 🗒️ Additional notes

✔️ **Manual version**: Run `ssh` and enter the password.  
✔️ **Intermediate version**: Save the server fingerprint in `~/.ssh/known_hosts` to avoid confirming it again.  
✔️ **Advanced version**: Use public key authentication instead of a password.