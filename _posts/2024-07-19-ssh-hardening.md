---
layout: post
title: "SSH Hardening: Securing Your Remote Connections"
tags: linux security
---

In the previous post, we covered the basics of SSH. In this post, we will go a bit deeper into SSH. 
As we all know Secure Shell (SSH) is a widely used protocol for securely accessing remote servers. However, the default SSH configuration is not always secure enough for sensitive environments. Hardening your SSH setup is essential to protect your servers from unauthorized access and attacks. This guide will walk you through various techniques and best practices for SSH hardening, complete with examples.

### Why Harden SSH?

SSH hardening reduces the risk of unauthorized access, brute-force attacks, and other security threats. By implementing stronger security measures, you ensure that only authorized users can access your servers and that your data remains secure during transit.

### Basic SSH Hardening Steps

#### 1. Update Your System

Before making any changes, ensure your system is up-to-date. This ensures you have the latest security patches and updates.

```bash
sudo apt update && sudo apt upgrade -y
```

#### 2. Change the Default SSH Port

Changing the default SSH port (22) can help obscure your SSH service from automated scans and reduce the likelihood of brute-force attacks.

1. **Open the SSH configuration file:**

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. **Change the port number:**

   ```plaintext
   Port 2222
   ```

3. **Save and close the file, then restart the SSH service:**

   ```bash
   sudo systemctl restart ssh
   ```

4. **Update your firewall rules to allow the new port:**

   ```bash
   sudo ufw allow 2222/tcp
   ```

#### 3. Disable Root Login

Allowing root login via SSH is a significant security risk. Disable it to enforce the principle of least privilege.

1. **Edit the SSH configuration file:**

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. **Disable root login:**

   ```plaintext
   PermitRootLogin no
   ```

3. **Save and restart the SSH service:**

   ```bash
   sudo systemctl restart ssh
   ```

#### 4. Use SSH Key Authentication

SSH keys provide a more secure authentication method compared to passwords.

1. **Generate an SSH key pair on your local machine:**

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

2. **Copy the public key to your server:**

   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub user@server_ip
   ```

3. **Disable password authentication:**

   - Edit the SSH configuration file:

     ```bash
     sudo nano /etc/ssh/sshd_config
     ```

   - Disable password authentication:

     ```plaintext
     PasswordAuthentication no
     ```

   - Save and restart the SSH service:

     ```bash
     sudo systemctl restart ssh
     ```

#### 5. Use Strong Encryption and MAC Algorithms

Ensure SSH uses strong encryption algorithms and message authentication codes (MACs).

1. **Edit the SSH configuration file:**

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. **Specify strong algorithms:**

   ```plaintext
   Ciphers aes256-ctr,aes192-ctr,aes128-ctr
   MACs hmac-sha2-512,hmac-sha2-256
   ```

3. **Save and restart the SSH service:**

   ```bash
   sudo systemctl restart ssh
   ```

### Advanced SSH Hardening Techniques

#### 1. Implement Two-Factor Authentication (2FA)

Adding an extra layer of security through two-factor authentication can significantly improve SSH security.

1. **Install Google Authenticator:**

   ```bash
   sudo apt install libpam-google-authenticator
   ```

2. **Configure Google Authenticator for your user:**

   ```bash
   google-authenticator
   ```

   Follow the prompts to set up 2FA.

3. **Configure SSH to use Google Authenticator:**

   - Edit the PAM configuration:

     ```bash
     sudo nano /etc/pam.d/sshd
     ```

   - Add the following line at the end:

     ```plaintext
     auth required pam_google_authenticator.so
     ```

   - Edit the SSH configuration file:

     ```bash
     sudo nano /etc/ssh/sshd_config
     ```

   - Enable Challenge-Response Authentication:

     ```plaintext
     ChallengeResponseAuthentication yes
     ```

   - Save and restart the SSH service:

     ```bash
     sudo systemctl restart ssh
     ```

#### 2. Limit User Access

Restrict which users can log in via SSH.

1. **Edit the SSH configuration file:**

   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. **Specify allowed users:**

   ```plaintext
   AllowUsers user1 user2
   ```

3. **Save and restart the SSH service:**

   ```bash
   sudo systemctl restart ssh
   ```

#### 3. Use a Banner to Warn Unauthorized Users

A warning banner can inform unauthorized users that they are not welcome.

1. **Edit the SSH banner file:**

   ```bash
   sudo nano /etc/issue.net
   ```

2. **Add your warning message:**

   ```plaintext
   Unauthorized access to this system is prohibited.
   ```

3. **Enable the banner in the SSH configuration file:**

   - Edit the SSH configuration file:

     ```bash
     sudo nano /etc/ssh/sshd_config
     ```

   - Add the following line:

     ```plaintext
     Banner /etc/issue.net
     ```

   - Save and restart the SSH service:

     ```bash
     sudo systemctl restart ssh
     ```

### Monitoring and Maintenance

#### 1. Enable and Monitor Logs

Regularly monitor SSH logs to detect any unusual activity.

- **SSH logs are typically located at `/var/log/auth.log` on Debian-based systems and `/var/log/secure` on Red Hat-based systems.**

  ```bash
  sudo tail -f /var/log/auth.log
  ```

#### 2. Use Fail2Ban

Fail2Ban helps protect against brute-force attacks by banning IP addresses that exhibit suspicious behavior.

1. **Install Fail2Ban:**

   ```bash
   sudo apt install fail2ban
   ```

2. **Create a local configuration file:**

   ```bash
   sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
   ```

3. **Configure SSH protection in the local configuration file:**

   ```bash
   sudo nano /etc/fail2ban/jail.local
   ```

   Ensure the `[sshd]` section is enabled and configured as follows:

   ```plaintext
   [sshd]
   enabled = true
   port = 2222
   filter = sshd
   logpath = /var/log/auth.log
   maxretry = 3
   ```

4. **Restart Fail2Ban:**

   ```bash
   sudo systemctl restart fail2ban
   ```

### Conclusion

SSH hardening is crucial for securing remote access to your servers. By following these steps and implementing best practices, you can significantly enhance the security of your SSH setup. Regularly review and update your configurations to stay ahead of potential threats.

For more detailed instructions and support, visit the [OpenSSH Documentation](https://www.openssh.com/manual.html).

