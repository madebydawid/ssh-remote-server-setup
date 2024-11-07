# Remote Linux Server Setup with SSH

## Overview
The goal of this project is to set up a remote Linux server and configure secure SSH access. An optional step includes installing Fail2ban to add protection against brute-force attacks.

## Requirements
- A cloud provider account (e.g., Azure, AWS) for the Linux server setup.
- Configured SSH access using *two separate SSH key pairs*.
- Alias configuration in `~/.ssh/config` for easier access.
- (Optional) `Fail2ban` for additional security.

## Steps

### 1. Set Up the Server

- [ ] Register and configure a remote Linux server on a cloud provider.
- [ ] Retrieve the server’s IP address and initial access credentials.

### 2. Generate SSH Key Pairs

- [ ] Generate two SSH key pairs on your local machine:
    ```bash
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_key1
    ```
    ```bash
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/azure_key2
    ```
    - **RSA encryption** with 4096 bits ensures strong security.
    - The `-f` flag specifies the file path for the key (adjust as needed).

> **Note**: You’ll be prompted for a passphrase, which adds extra security. It can be left empty, but using a passphrase is **recommended**.

![Model](https://github.com/madebydawid/ssh-remote-server-setup/blob/main/images/ssh-key-creation.jpg?raw=true)
![Model](https://github.com/madebydawid/ssh-remote-server-setup/blob/main/images/both-ssh-keys.jpg?raw=true)

### 3. Add SSH Keys to the Server

- [ ] Copy the contents of each `.pub` file (public keys) to the server.
    - **View each `.pub` file’s content**:
        ```bash
        cat ~/.ssh/azure_key1.pub
        cat ~/.ssh/azure_key2.pub
        ```

- **Connect to the server** (initially via password or an existing key).
- **On the server**:
    1. Navigate to (or create) the `.ssh` directory:
        ```bash
        mkdir -p ~/.ssh # Create directory
        chmod 700 ~/.ssh # Set directory permissions
        ```
    2. **Add public keys** to the `authorized_keys` file:
        ```bash
        echo "your-public-key" >> ~/.ssh/authorized_keys
        ```
        Repeat for each key. Set correct permissions for the `authorized_keys` file:
        ```bash
        chmod 600 ~/.ssh/authorized_keys # Set permissions to read/write for the owner only
        ```

### 4. Configure and Test SSH Connections

- [ ] Test connecting to the server with each key:
    ```bash
    ssh -i ~/.ssh/azure_key1 username@server-ip
    ssh -i ~/.ssh/azure_key2 username@server-ip
    ```
- Verify that both key pairs allow successful connections.

### 5. Configure an SSH Alias

- [ ] Set up an alias in the `~/.ssh/config` file to simplify future connections.

1. Open or create the `~/.ssh/config` file:
    ```bash
    nano ~/.ssh/config
    ```
2. Add alias configuration as shown below:
    ```plaintext
    Host myserver
        HostName server-ip
        User username
        IdentityFile ~/.ssh/azure_key1
    ```
    - `Host` is the alias name you’ll use for the command, e.g., `myserver`.
    - `HostName` is the server’s IP address.
    - `User` is the username on the server.
    - `IdentityFile` is the path to the private key you want to use (in this case, `azure_key1`).

> To use both keys, add a separate block for `azure_key2` with a different alias.

![Model](https://github.com/madebydawid/ssh-remote-server-setup/blob/main/images/cat-config.png?raw=true)

### 6. Test SSH Connection with Alias

- [ ] Test the alias connection:
    ```bash
    ssh myserver
    ```
  
![Model](https://github.com/madebydawid/ssh-remote-server-setup/blob/main/images/ssh-config-login.png?raw=true)

### 7. Install Fail2ban for Enhanced Security (Optional)

- [ ] Install Fail2ban to protect SSH:
    ```bash
    sudo apt update
    sudo apt install fail2ban
    ```

- [ ] Configure Fail2ban for SSH by editing the config file:
    ```bash
    sudo nano /etc/fail2ban/jail.local
    ```

- [ ] Add the following to `jail.local` to protect SSH:
    ```bash
    [DEFAULT]
    bantime = 10m           # How long an IP is blocked (10 minutes)
    findtime = 10m          # Timeframe to check for failed attempts
    maxretry = 5            # Max failed attempts before banning
    logpath = /var/log/auth.log # Path where failed attempts are logged

    [sshd]
    enabled = true          # Activate Fail2ban for SSH
    ```

- [ ] Restart Fail2ban to apply the changes:
    ```bash
    sudo systemctl restart fail2ban
    sudo systemctl enable fail2ban
    ```

![Model](https://github.com/madebydawid/ssh-remote-server-setup/blob/main/images/fail2ban.png?raw=true)

---

[Link to Roadmap.sh Project](https://roadmap.sh/projects/ssh-remote-server-setup)
