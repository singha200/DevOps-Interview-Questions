# Automation & Scripting Interview Questions

## **Beginner-Level (1-20) Questions**  

### **1. What is automation in DevOps?**  

Automation in DevOps refers to scripting repetitive tasks like provisioning, configuration, deployment, and monitoring to improve efficiency and reduce errors.  

### **2. What are the benefits of scripting in DevOps?**  

- Reduces manual effort  
- Increases consistency and repeatability  
- Improves efficiency and speed  
- Reduces errors and enhances security  

### **3. What is Bash scripting?**  

Bash scripting is writing command-line instructions in a script file (`.sh`) to automate tasks in Unix/Linux environments.  

### **4. How do you write a basic Bash script?**  

```bash
#!/bin/bash
echo "Hello, DevOps!"
```

Save the file (`script.sh`), make it executable (`chmod +x script.sh`), and run it (`./script.sh`).  

### **5. What is the difference between Bash and Shell scripting?**  

Bash is a type of shell, but shell scripting can also be done in other shells like **sh, csh, and zsh**. Bash provides more advanced scripting features.  

### **6. What are variables in Bash?**  

Variables store values and are defined without a `$` sign but accessed using `$`.  

```bash
name="DevOps"
echo "Hello, $name"
```

### **7. What is Python scripting used for in DevOps?**  

- Infrastructure as Code (IaC)  
- CI/CD automation  
- Log analysis  
- Cloud automation (AWS, Azure, GCP SDKs)  

### **8. How do you define a function in Python?**  

```python
def greet():
    print("Hello, DevOps!")
greet()
```

### **9. What is YAML, and where is it used?**  

YAML (Yet Another Markup Language) is a human-readable format used for **Kubernetes configurations, Ansible playbooks, CI/CD pipelines**, etc.  

### **10. What is a YAML file example?**  

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

### **11. How do you write a simple Groovy script?**  

```groovy
println "Hello, DevOps!"
```

Groovy is used in **Jenkins pipelines and automation tasks**.  

### **12. What is the shebang (`#!`) in a script?**  

The shebang (`#!/bin/bash` or `#!/usr/bin/python3`) specifies the interpreter for executing the script.  

### **13. What are loops in Bash?**  

Bash supports `for`, `while`, and `until` loops. Example:  

```bash
for i in {1..5}; do echo "Iteration $i"; done
```

### **14. What are conditional statements in Bash?**  

`if-else` statements execute different code based on conditions.  

```bash
if [ $USER == "root" ]; then echo "Admin access"; else echo "User access"; fi
```

### **15. How do you read input in Bash?**  

```bash
echo "Enter name: "
read name
echo "Hello, $name"
```

### **16. How do you create a Python virtual environment?**  

```bash
python3 -m venv myenv
source myenv/bin/activate
```

### **17. How do you parse JSON in Python?**  

```python
import json
data = '{"name": "DevOps"}'
parsed = json.loads(data)
print(parsed["name"])
```

### **18. What is the `awk` command in Bash?**  

`awk` is used for text processing. Example:  

```bash
awk '{print $1}' file.txt
```

Extracts the first column from `file.txt`.  

### **19. How do you comment in YAML?**  

Use `#` for comments.  

```yaml
# This is a comment
name: DevOps
```

### **20. How do you declare variables in Groovy?**  

```groovy
def name = "DevOps"
println name
```

---

## **Intermediate-Level (21-40) Questions**  

### **21. How do you pass arguments to a Bash script?**  

```bash
#!/bin/bash
echo "Hello, $1!"
```

Run: `./script.sh DevOps` → Output: `Hello, DevOps!`  

### **22. How do you handle errors in Bash scripts?**  

Use `set -e` to stop execution on errors.  

### **23. What is an Ansible playbook?**  

A YAML file that defines automation tasks for servers.  

### **24. How do you handle exceptions in Python?**  

```python
try:
    print(1 / 0)
except ZeroDivisionError:
    print("Cannot divide by zero")
```

### **25. How do you schedule a script with Cron?**  

Edit `crontab -e` and add:  

```bash
0 5 * * * /path/to/script.sh
```

Runs the script daily at 5 AM.  

### **26. How do you execute a Groovy script in Jenkins?**  

Use `script {}` inside a Jenkins pipeline.  

### **27. How do you create a list in Python?**  

```python
mylist = [1, 2, 3]
print(mylist[0])
```

### **28. What is `sed` in Bash?**  

Used for text replacement. Example:  

```bash
sed -i 's/old/new/g' file.txt
```

### **29. How do you define a dictionary in Python?**  

```python
mydict = {"name": "DevOps"}
print(mydict["name"])
```

### **30. How do you validate a YAML file?**  

Use `yamllint` or `kubectl apply -f --dry-run=client`.  

### **31. How do you install Python modules?**  

```bash
pip install requests
```

### **32. What is Jenkins pipeline syntax for automation?**  

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Building..."
            }
        }
    }
}
```

### **33. How do you iterate over a dictionary in Python?**  

```python
for key, value in mydict.items():
    print(key, value)
```

### **34. How do you set environment variables in Bash?**  

```bash
export VAR="DevOps"
```

### **35. What is an Ansible role?**  

A reusable way to organize Ansible tasks, handlers, and templates.  

### **36. What is a multiline string in YAML?**  

```yaml
message: |
  Line 1
  Line 2
```

### **37. What is an associative array in Bash?**  

```bash
declare -A myarray
myarray[name]="DevOps"
echo ${myarray[name]}
```

### **38. How do you run a shell command in Python?**  

```python
import os
os.system("ls")
```

### **39. What is `jq` in Linux?**  

Used to parse JSON. Example:  

```bash
cat data.json | jq '.name'
```

### **40. How do you exit a script with a status code?**  

```bash
exit 1
```

---

## **Advanced-Level (41-60) Questions**  

### **41. How do you debug a Bash script?**  

Use `set -x` for debugging:  

```bash
#!/bin/bash
set -x
echo "Debugging mode enabled"
```

### **42. How do you trap signals in a Bash script?**  

```bash
trap "echo 'Script interrupted'; exit" SIGINT SIGTERM
```

Catches `Ctrl+C` (SIGINT) and terminates gracefully.  

### **43. What is the difference between `$(command)` and backticks `` `command` `` in Bash?**  

Both execute commands, but `$(command)` is preferred as it's **nestable**.  

### **44. How do you handle multiline commands in a Bash script?**  

Use `\` for line continuation:  

```bash
echo "This is a \
multiline command"
```

### **45. How do you use conditionals inside a YAML file?**  

With Jinja2 templating in Ansible:  

```yaml
tasks:
  - name: Install package
    yum:
      name: httpd
    when: ansible_os_family == "RedHat"
```

### **46. How do you encrypt secrets in YAML?**  

Use **Ansible Vault**:  

```bash
ansible-vault encrypt secrets.yaml
```

### **47. How do you execute a Python script inside a Bash script?**  

```bash
python3 <<EOF
print("Hello from Python")
EOF
```

### **48. What is the difference between `continue` and `break` in Bash loops?**  

- `break` exits the loop entirely.  
- `continue` skips the current iteration.  

### **49. How do you parse a JSON file in Bash?**  

Use `jq`:  

```bash
cat data.json | jq '.key'
```

### **50. How do you use arrays in Groovy?**  

```groovy
def arr = ["a", "b", "c"]
println arr[0]
```

### **51. What is a Jenkins shared library in Groovy?**  

A reusable **pipeline function** stored in Git.  

### **52. How do you set a timeout for a script in Bash?**  

```bash
timeout 10s ./script.sh
```

### **53. How do you execute a Bash function in a subshell?**  

```bash
(my_function)
```

Runs in a new shell, not affecting the parent script.  

### **54. How do you use Python to send an HTTP request?**  

```python
import requests
response = requests.get("https://example.com")
print(response.text)
```

### **55. How do you handle authentication in a Python script?**  

```python
import requests
requests.get("https://example.com", auth=("user", "pass"))
```

### **56. How do you find and replace text in YAML files?**  

Use `yq`:  

```bash
yq e '.name="NewValue"' -i config.yaml
```

### **57. How do you define a multi-stage Jenkins pipeline in Groovy?**  

```groovy
pipeline {
    agent any
    stages {
        stage('Build') { steps { echo "Building..." } }
        stage('Test') { steps { echo "Testing..." } }
    }
}
```

### **58. What is an inline script in a CI/CD pipeline?**  

Running a script **directly inside a pipeline** (e.g., GitHub Actions, Jenkins).  
Example in GitHub Actions:  

```yaml
jobs:
  build:
    steps:
      - run: echo "Inline script execution"
```

### **59. How do you execute a script remotely via SSH in Bash?**  

```bash
ssh user@server 'bash -s' < local_script.sh
```

### **60. How do you ensure idempotency in an Ansible playbook?**  

Ansible modules ensure **idempotency** by only making changes when needed.  

---

### **61. ## What are some common Python packages that you use as a DevOps Engineer?

### Answer Some commonly used Python packages for DevOps Engineers include `boto3`, `paramiko`, `requests`, `pyyaml`, `docker`, `kubernetes`, `fabric`, and `pytest`.

### Detailed explanation (with examples)

1. **boto3** – AWS SDK for Python  
   Used to automate and manage AWS services.
   
```
   Example:
       import boto3
       ec2 = boto3.client('ec2')
       response = ec2.describe_instances()
       print(response)
```

2. **paramiko** – SSH and remote command execution  
   Useful for running remote shell commands or transferring files via SFTP.
   
```
   Example:
       import paramiko
       ssh = paramiko.SSHClient()
       ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
       ssh.connect(hostname='remote.server.com', username='user', password='pass')
       stdin, stdout, stderr = ssh.exec_command('uptime')
       print(stdout.read().decode())
       ssh.close()
```

3. **requests** – HTTP requests  
   Used for interacting with REST APIs and webhooks.

```
   Example:
       import requests
       response = requests.get('https://api.example.com/status')
       print(response.status_code)
       print(response.json())
```

4. **pyyaml** – YAML parsing and generation  
   Very useful when dealing with Kubernetes manifests or configuration files.

```
   Example:
       import yaml
       with open('config.yaml', 'r') as file:
           config = yaml.safe_load(file)
       print(config)
```

5. **docker** – Docker SDK for Python  
   Used to manage Docker containers, images, and volumes programmatically.

```
   Example:
       import docker
       client = docker.from_env()
       for container in client.containers.list():
           print(container.name, container.status)
```

6. **kubernetes** – Kubernetes Python client  
   Helps in automating Kubernetes resource creation, deletion, and monitoring.

```
   Example:
       from kubernetes import client, config
       config.load_kube_config()
       v1 = client.CoreV1Api()
       pods = v1.list_pod_for_all_namespaces()
       for pod in pods.items:
           print(pod.metadata.name)
```

7. **fabric** – High-level SSH command execution  
   Simplifies automation tasks on remote servers.

```
   Example:
       from fabric import Connection
       c = Connection('user@host')
       c.run('uname -a')
```

8. **pytest** – Python testing framework  
   Useful for writing automated tests for infrastructure or config scripts.

```
   Example:
       def add(a, b):
           return a + b

       def test_add():
           assert add(2, 3) == 5
```

### **62. Python script to run a container using Docker SDK - User provides image name as input

### Answer Use `docker.from_env()` to connect to the local Docker engine and run the container with the provided image name.

### Detailed explanation (with examples)

Here is the complete Python script:

```
import docker

# Initialize Docker client
client = docker.from_env()

# Get image name from user input
image_name = input("Enter the Docker image name (e.g., nginx:latest): ").strip()

try:
    # Pull the image (if not present locally)
    print(f"Pulling image '{image_name}'...")
    client.images.pull(image_name)
    print(f"Image '{image_name}' pulled successfully.")

    # Run the container
    print(f"Running container from image '{image_name}'...")
    container = client.containers.run(image_name, detach=True)
    print(f"Container started with ID: {container.id[:12]}")

except docker.errors.ImageNotFound:
    print(f"Error: Image '{image_name}' not found.")
except docker.errors.APIError as e:
    print(f"Docker API error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
```

### Example usage

When you run the script:

    Enter the Docker image name (e.g., nginx:latest): alpine

Expected output:

    Pulling image 'alpine'...
    Image 'alpine' pulled successfully.
    Running container from image 'alpine'...
    Container started with ID: 3e1fabc2d789

This script assumes Docker is installed and the user has permission to run Docker commands. It runs the container in the background (`detach=True`) without any specific command or port mapping.

### **63 ## Python script to fetch logs from a log website and print all logs with 404: not found

### Answer - Use Python’s `requests` library to fetch the log file from a public URL and filter for lines containing `404`.

### Detailed explanation (with examples)

We’ll use a real log sample from GitHub that mimics Apache HTTP access logs. Example log source:
`https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs`

Here’s the complete script:

```
import requests

# Publicly available Apache log sample
log_url = 'https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs'

try:
    # Fetch the log content
    response = requests.get(log_url)
    response.raise_for_status()
    logs = response.text.splitlines()

    print("Log lines with 404 Not Found:\n")

    # Search for lines with HTTP 404
    for line in logs:
        if ' 404 ' in line:
            print(line)

except requests.exceptions.RequestException as e:
    print(f"Error fetching logs: {e}")
```

### Example matching line from the log:

    216.46.173.126 - - [27/May/2015:10:27:47 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 404 146

This script:
- Fetches logs using `requests.get()`
- Splits the logs line by line
- Filters for those containing ` 404 ` to avoid false matches in URLs or timestamps
- Prints all matching lines to the console

You can modify the script to write the filtered lines to a file or analyze other HTTP status codes like `500`, `403`, etc.
