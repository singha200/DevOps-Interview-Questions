## Question 1 
What are 10 Linux commands you use daily? (Excluding basic ones like `ls` and `cd`)

## ✅ Answer  
Here are 10 Linux commands I use regularly, excluding the basics like `cd`, `ls`, and `pwd`:

---

### 1. `tail -f`  
```bash
tail -f /var/log/nginx/error.log
```
🔍 Monitor log files in real time — very useful for debugging issues as they happen.

---

### 2. `grep`  
```bash
grep -i "timeout" /var/log/app.log
```
🔎 Search through files, logs, or command outputs for specific patterns. I use this to quickly isolate errors.

---

### 3. `systemctl`  
```bash
systemctl restart nginx
```
🛠️ Control system services — starting, stopping, checking status of systemd services.

---

### 4. `journalctl`  
```bash
journalctl -u docker.service -f
```
🧾 View logs for systemd-managed services. Especially handy for debugging issues with services like Docker or Kubelet.

---

### 5. `ps aux | grep`  
```bash
ps aux | grep nginx
```
📋 List running processes. I use this to find rogue or resource-intensive processes.

---

### 6. `df -h` / `du -sh`  
```bash
df -h       # Check available disk space  
du -sh *    # See folder sizes in current directory
```
💾 Essential for disk space monitoring and cleaning up large files or folders.

---

### 7. `chmod` / `chown`  
```bash
chmod +x deploy.sh  
chown ubuntu:ubuntu script.sh
```
🔐 Manage file permissions and ownership — very common in CI/CD and provisioning tasks.

---

### 8. `find`  
```bash
find /var/log -name "*.log" -mtime +7
```
🔍 Locate files based on name, date, type, etc. Great for automating cleanup or audits.

---

### 9. `curl`  
```bash
curl -I http://localhost:8080
```
🌐 Test web endpoints, APIs, or service availability from the command line.

---

### 10. `rsync`  
```bash
rsync -avz /app/ user@server:/backup/
```
📁 Efficient file syncing and backup — much faster and more reliable than `scp` for large directories.

### 11. `nc`
```bash
nc -zv localhost 80
```
🔌 Network utility to check if a port is open or to send data over the network.
---

## Question 2  
Can you restore a lost PEM file? If not, how can you still access the EC2 instance?

## ✅ Answer  
No, you **cannot restore** a lost PEM file — it’s not stored on AWS or recoverable.  
However, you can **regain access** by using a workaround: create a new key pair, attach it to the instance via a temporary EC2 rescue process, and restore SSH access.

PEM files (private keys) are **never retrievable from AWS** after initial creation. Losing the PEM file means you cannot SSH into the EC2 instance using the existing key pair.

But here’s how you can still regain access:

---

### ✅ Recovery Steps:

#### Step 1: Create a new key pair
```bash
aws ec2 create-key-pair --key-name new-key --query 'KeyMaterial' --output text > new-key.pem
chmod 400 new-key.pem
```

---

#### Step 2: Stop the affected instance  
In the AWS Console or CLI:
```bash
aws ec2 stop-instances --instance-ids i-xxxxxxxxxxxxxxx
```

---

#### Step 3: Detach the root EBS volume from the stopped instance

- Go to **EC2 > Volumes**
- Find the volume attached to your instance (usually `/dev/xvda`)
- **Detach** it

---

#### Step 4: Attach the volume to a temporary (working) instance  
- Attach it as a secondary volume (e.g., `/dev/sdf`)

---

#### Step 5: SSH into the temporary instance  
Mount the volume:
```bash
sudo mkdir /mnt/recovery
sudo mount /dev/xvdf1 /mnt/recovery
```

Edit the `authorized_keys` file on the broken instance's volume:
```bash
sudo nano /mnt/recovery/home/ec2-user/.ssh/authorized_keys
```

Add the **public key** from your new key pair (`new-key.pub`)

---

#### Step 6: Detach the volume from the rescue instance  
- Unmount the volume  
- Detach it and re-attach to the original instance as `/dev/xvda`

---

#### Step 7: Start the original instance and SSH with new key  
```bash
ssh -i new-key.pem ec2-user@<public-ip>
```

---

### 🧠 Prevention Tips:

- Always back up PEM files in a secure location (like a password manager).
- Create a **secondary user** with a different key for backup access.
- Use EC2 Instance Connect for temporary browser-based access (only works for Amazon Linux 2+ and enabled roles).

> Summary:  
> You cannot restore a PEM file, but you can regain access by editing the `authorized_keys` on the root volume through a temporary rescue EC2 instance.

---

## Question 3 
`/var` is almost 90% full. What will be your next steps?

## ✅ Answer  
My first step is to identify what’s consuming the space inside `/var`. Then I would clean up unnecessary files like rotated logs, caches, or orphaned packages — and put alerts or log rotation in place to avoid recurrence.
---

### ✅ Step 1: Inspect Disk Usage Under `/var`

```bash
sudo du -sh /var/* | sort -hr | head -10
```
This will show which directories inside `/var` are consuming the most space — usually it’s `/var/log`, `/var/cache`, or `/var/lib/docker`.

---

### ✅ Step 2: Clean Log Files  
If `/var/log` is the culprit:

```bash
sudo journalctl --vacuum-size=200M
sudo rm -rf /var/log/*.gz /var/log/*.[0-9]
```

Or truncate large log files:
```bash
sudo truncate -s 0 /var/log/syslog
```

---

### ✅ Step 3: Clear Package Cache  
If using `apt` or `yum`, clear the package manager cache:

```bash
sudo apt clean         # Debian/Ubuntu
sudo yum clean all     # RHEL/CentOS
```

---

### ✅ Step 4: Check Docker Artifacts  
If the server runs containers:

```bash
docker system df        # See what’s taking space
docker system prune -a  # Remove unused containers/images
```

**⚠️ Warning:** Prune removes *unused* images and volumes — be cautious on production systems.

---

### ✅ Step 5: Consider Moving or Archiving Data  
If data in `/var` is needed but rarely accessed:
- Archive old logs to `/home` or S3
- Use `logrotate` to compress and limit logs:
  ```bash
  sudo nano /etc/logrotate.conf
  ```

---

### ✅ Step 6: Set Up Alerts and Monitoring  
- Install `ncdu`, `duf`, or setup Prometheus/Grafana alerts for disk usage thresholds.
- Automate cleanup with cron or systemd timers if appropriate.

---

### 🧠 Why `/var` Fills Up:
- Verbose logging (e.g., failed cron jobs, app debug logs)
- Docker images/layers
- Orphaned cache files
- Email spools or crash dumps

> Summary:  
> Quickly inspect, clean, and automate monitoring. Ensure critical services like journald, docker, and package managers are not starved of space.

---

## Question 4
Linux Server is slow due to high CPU utilization. How will you fix it?

## ✅ Answer  
I would begin by identifying which processes are consuming the most CPU using tools like `top`, `htop`, or `pidstat`, then analyze whether it's due to a misbehaving application, runaway process, or scheduled job. Based on the findings, I’d take corrective action — either by killing the process, adjusting resource limits, or scaling the workload.

### 📘 Detailed Explanation  

---

### ✅ Step 1: Check Load Average  
```bash
uptime
```
Example output:
```
14:02:03 up  3 days,  4:55,  2 users,  load average: 6.02, 4.33, 2.89
```
A load average consistently higher than the number of CPU cores indicates overutilization.

---

### ✅ Step 2: Identify CPU-Heavy Processes  
```bash
top -o %CPU
```
or more interactively:
```bash
htop
```

This shows which processes are consuming the most CPU.

---

### ✅ Step 3: Drill Down with `ps` or `pidstat`  
```bash
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head
```

or:
```bash
pidstat -u 1 5
```

These give detailed insight into CPU consumption over time.

---

### ✅ Step 4: Investigate the Cause  
Based on what you see, ask:
- Is it a specific app (e.g., Java, Python, Node.js)?
- Is there a cron job or batch script running?
- Is a service misconfigured and looping?
- Is it caused by a known bug (e.g., zombie processes)?

---

### ✅ Step 5: Take Corrective Action  
- Kill or restart runaway process:
  ```bash
  kill -9 <pid>
  systemctl restart <service>
  ```
- Scale the application or move workloads
- Limit resource usage using `nice`, `cpulimit`, or cgroups
- Tune app performance (e.g., DB queries, memory leaks)

---

### ✅ Step 6: Check Logs  
```bash
journalctl -xe
tail -f /var/log/syslog
```
Logs may reveal:
- App crashes
- High retry loops
- Configuration issues

---

### ✅ Step 7: Implement Preventive Measures  
- Set CPU/memory limits in containerized apps
- Use monitoring tools like `Prometheus + Grafana`
- Configure alerts for high CPU (e.g., above 80% for 5 mins)
- Refactor long-running or expensive tasks

---

### 🧠 Real-Life Examples:
- A cron script looping due to a bad condition
- A Java app stuck in infinite recursion
- Docker containers running unbounded scraping jobs
- Antivirus or audit daemon consuming CPU after log floods

> Summary:  
> Use `top`, `htop`, `ps`, and `pidstat` to identify heavy processes. Fix the root cause and add monitoring to avoid similar issues in the future.

---

## Question 5 
Application deployed on NGINX returns "Connection Refused". How will you fix it?

## ✅ Answer  
I would first check whether NGINX itself is running and listening on the correct port, then verify that the application backend is also up and accessible. It could be a misconfiguration in the NGINX config or the application not listening on the expected socket or port.

---

### ✅ Step 1: Reproduce the Error  
Try to access the app from the browser or use:
```bash
curl -I http://localhost
```
If you get:
```
curl: (7) Failed to connect to localhost port 80: Connection refused
```
It confirms the server refused the TCP handshake — not a 4xx/5xx error.

---

### ✅ Step 2: Check if NGINX is Running  
```bash
sudo systemctl status nginx
```
If it’s inactive or failed:
```bash
sudo systemctl restart nginx
sudo journalctl -u nginx -xe
```

---

### ✅ Step 3: Is NGINX Listening on the Expected Port?  
```bash
sudo netstat -tulnp | grep nginx
```
or:
```bash
ss -tuln | grep :80
```
No output? Then NGINX is not listening on the port you're accessing.

---

### ✅ Step 4: Check NGINX Configuration  
```bash
sudo nginx -t
```
This tests the NGINX config for syntax errors.

Also verify your `/etc/nginx/sites-enabled/default` or your custom config:

```nginx
server {
    listen 80;
    location / {
        proxy_pass http://localhost:5000;  # Is your app running here?
    }
}
```

---

### ✅ Step 5: Verify the Application Backend  
If NGINX is trying to proxy to `http://localhost:5000`, is your app actually running on that port?

```bash
sudo netstat -tulnp | grep 5000
curl http://localhost:5000
```

If this fails, restart or debug your app.

---

### ✅ Step 6: Check Firewall/Security Groups (Cloud Hosts)  
On cloud VMs, make sure the port is open in:
- AWS Security Group
- GCP firewall rules
- `ufw` or `iptables` on the VM

```bash
sudo ufw status
sudo iptables -L
```

---

### ✅ Step 7: Look for SELinux or AppArmor Restrictions  
If using SELinux:
```bash
sudo getenforce
```
If it's `Enforcing`, and ports/services are restricted, update policies or temporarily disable for testing.

---

### 🧠 Common Real-Life Causes:
- App crashed or not listening on correct port
- Wrong proxy_pass value in NGINX
- Port blocked by firewall
- NGINX service not restarted after config change
- App takes too long to start — NGINX proxies fail

> Summary:  
> Check if NGINX is running, verify app backend availability, inspect NGINX configs, and ensure network rules allow traffic. Fix any misalignment between proxy settings and actual service ports.

---

## Question 6
## SSH to an instance stopped working. How will you troubleshoot the issue?

## ✅ Answer  
I would follow a step-by-step process to identify whether the issue is with networking (e.g., security group), the instance itself (e.g., crashed SSH service), or the credentials (e.g., PEM file or key mismatch). Based on the findings, I would take corrective actions accordingly.

---

### ✅ Step 1: Confirm the Error Message  
From your local machine:
```bash
ssh -i my-key.pem ec2-user@<instance-public-ip>
```

Typical errors:
- `Permission denied (publickey)`
- `Connection refused`
- `Operation timed out`

The error gives the first clue.

---

### ✅ Step 2: Check Instance Health & Reachability
- Is the instance **running**?
- Is it **reachable**?
  
Use AWS Console or CLI:
```bash
aws ec2 describe-instance-status --instance-id <id>
ping <public-ip>
```

If instance is unreachable → investigate VPC/subnet/NACL routing issues.

---

### ✅ Step 3: Verify Security Group Rules  
Make sure port 22 is open **from your IP**:
```text
Inbound rule:
Type: SSH
Port: 22
Source: your IP (e.g., 203.0.113.0/32)
```

If using a **bastion host**, check its connectivity as well.

---

### ✅ Step 4: Check Network ACLs & Route Tables  
Ensure NACLs are not blocking traffic and public subnet has a route to internet gateway.

---

### ✅ Step 5: Confirm Public IP or Elastic IP  
Check if the instance has a **public IP** or **Elastic IP** attached.  
Elastic IPs don’t change, but public IPs do if the instance is stopped and started.

---

### ✅ Step 6: Validate PEM File & User  
Make sure:
- The PEM file is correct (`chmod 400`)
- You're using the right username:
  - `ec2-user` for Amazon Linux
  - `ubuntu` for Ubuntu
  - `centos` for CentOS

---

### ✅ Step 7: Try EC2 Instance Connect (Amazon Linux only)  
If the PEM is lost or SSH doesn't work:
- Use **EC2 Instance Connect** via AWS Console
- Once inside, you can check:
  ```bash
  sudo systemctl status sshd
  tail -n 50 /var/log/auth.log  # or secure/log
  ```

---

### ✅ Step 8: Rescue Mode (Advanced)  
If all else fails:
- Stop the instance
- Detach the root volume
- Attach it to another instance
- Mount it and edit `~/.ssh/authorized_keys` or repair config
- Reattach and start the original instance

---

### 🧠 Real-Life Causes I’ve Faced:
- Team used a wrong key pair name
- Security group updated accidentally
- User tried SSH with wrong username (e.g., root)
- Instance rebooted with new IP and old DNS cached
- `/etc/ssh/sshd_config` edited incorrectly

> Summary:  
> Identify error → check network reachability → inspect key/user mismatch → use EC2 Connect if possible → rescue via EBS if needed.

---

## Question 7
