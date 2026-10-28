# SSH-Root

In RHEL, you can enable SSH login for the `root` user by modifying the SSH server configuration.

### Step 1: Set a root password

Log in as a user with sudo privileges and run:

```bash
sudo passwd root
```

Enter and confirm the new root password.

### Step 2: Edit the SSH configuration

Open the SSH server configuration file:

```bash
sudo vi /etc/ssh/sshd_config
```

Find these settings:

```text
#PermitRootLogin prohibit-password
#PasswordAuthentication yes
```

Set them to:

```text
PermitRootLogin yes
PasswordAuthentication yes
```

Important: Remove the `#` if present. If the options appear multiple times, check the effective configuration because SSH settings can also be overridden by included configuration files.

In `vi`, press `i` to edit, then press `Esc`, type `:wq`, and press Enter to save.

### Step 3: Restart the SSH service

```bash
sudo systemctl restart sshd
```

Verify that SSH is running:

```bash
sudo systemctl status sshd
```

### Step 4: Check the firewall

If you're connecting remotely and firewalld is enabled, allow SSH:

```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

Also check any cloud security group or network firewall if this is an AWS EC2 instance.

### Step 5: Connect as root

From another machine:

```bash
ssh root@SERVER_IP
```

Enter the root password when prompted.

### Step 6: Verify the effective SSH settings

```bash
sudo sshd -T | grep -E 'permitrootlogin|passwordauthentication'
```

Expected output:

```text
permitrootlogin yes
passwordauthentication yes
```

Security recommendation: Direct root login using a password is risky, especially on an internet-facing server. Prefer SSH key authentication, a normal user with `sudo`, and `PermitRootLogin prohibit-password` where appropriate. Some cloud images also restrict root login by default.
