# Connecting Local Machine to a Remote SSH Server

*Lab Report*

| | |
|---|---|
| **Name** | Jesswin Anto J |
| **Date** | 22 July 2026 |
| **Objective** | Establish a secure connection from a local machine to a remote SSH server using two methods: the native SSH command and the PuTTY client. |

**Methods Covered**
- **Method 1: Connecting via SSH (Linux/WSL terminal)**
- **Method 2: Connecting via PuTTY (key conversion and GUI setup)**

---

## Task 1 — Connecting via SSH

### Step 1: Commands Used

```
0. cd /opt

1. sudo cp /mnt/c/Users/Jesswin/OneDrive/Documents/fita/cloud/instance_keys/web-server-ubuntu-2-ed25519-key-pair.pem .

2. sudo chmod 600 web-server-ubuntu-2-ed25519-key-pair.pem

3. sudo ssh -i "web-server-ubuntu-2-ed25519-key-pair.pem" ubuntu@13.207.84.105
```

### Command Syntax Explained
- **sudo —** Runs the command with superuser (administrator) privileges.
- **cp \<source_file_path\> \<destination_path\> —** Copies the specified file from the source path to the destination path.
- **chmod \<permissions\> \<file_name\> —** Changes the file's permission mode (e.g., 600 restricts access to the owner only, which SSH requires for key files).
- **ssh -i \<private_key_file\> \<username\>@\<server_ip\> —** Establishes a secure shell connection to the remote server, using -i to specify the private key for authentication.

*Figure 1: Successful SSH connection to the remote Ubuntu server.*

---

## Task 2 — Connecting via PuTTY

### Step 1: Converting the .pem Key to .ppk Format

Since PuTTY does not natively support .pem files, the key must first be converted to .ppk format using **PuTTYgen**.

1. Open PuTTYgen.
2. Click Load and select the .pem key file.
3. Set the parameter type to EdDSA.
4. Click Save private key to generate the .ppk file.

*Figure 2: Loading the .pem key in PuTTYgen.*
*Figure 3: Selecting the EdDSA key parameter.*
*Figure 4: Saving the converted private key (.ppk).*

### Step 2: Configuring and Launching PuTTY

1. **Host Name:** ubuntu@\<instance IP address\>
2. **Category settings —** Connection → SSH → Auth → Credentials → set the private key file path (.ppk).
3. **Session → Saved Sessions —** enter a session name and click Save.
4. **Select the saved session, click Load, then click Open.**
5. Accept the server's host key fingerprint when prompted to complete the connection.

*Figure 5: PuTTY session configuration.*
*Figure 6: Specifying the private key file under SSH Auth settings.*
*Figure 7: Saving the PuTTY session.*
*Figure 8: Loading the saved session.*
*Figure 9: Accepting the host key prompt.*
*Figure 10: Successful connection established via PuTTY.*

---

## Summary
- **Both methods successfully established a secure connection to the remote server.**
- **The SSH terminal method is faster for Linux/WSL users; PuTTY is preferable on Windows systems without a native SSH client.**
- **Correct key file permissions (chmod 600) and accurate key conversion (PuTTYgen) are critical for a successful connection.**
