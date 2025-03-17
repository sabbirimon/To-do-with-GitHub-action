# To-do-with-GitHub-action
 **Enhanced README.md** that now includes **SSH key authentication setup** for **VS Code, Terminal, and GitHub CLI**. This section will help you securely connect to GitHub without repeatedly entering your username and password.  

---

# **🚀 GitHub Actions Lab: To-Do List Automation & Secure Authentication**

This lab will help you automate **task management** using **GitHub Actions**, and securely authenticate using **SSH keys** from VS Code, Terminal, or other tools.

---

# **🔐 Setting Up Authentication with SSH Key**
## **Why Use SSH for GitHub?**
🔹 **Secure authentication** without passwords  
🔹 **Faster Git operations** (compared to HTTPS authentication)  
🔹 **Better integration** with VS Code and CI/CD tools  

---

## **📌 Step 1: Generate an SSH Key (Linux/Mac/Windows)**
Run the following command in **your terminal**:

```sh
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```

Press `Enter` to accept the default location (`~/.ssh/id_rsa`).  
🔑 **DO NOT** add a passphrase if you want **automated authentication**.  

Now, view your SSH key:

```sh
cat ~/.ssh/id_rsa.pub
```

Copy the output. It looks something like this:

```
ssh-rsa AAAAB3Nza...your-ssh-key... user@hostname
```

---

## **📌 Step 2: Add SSH Key to GitHub**
1. Go to **GitHub** → `Settings` → `SSH and GPG keys` → `New SSH Key`
2. **Title:** `My Dev Machine`
3. **Key Type:** `Authentication Key`
4. **Paste** the copied SSH key (`id_rsa.pub`)
5. Click **"Add SSH Key"** ✅  

---

## **📌 Step 3: Configure SSH Agent (Mac/Linux)**
Start the SSH agent and add the key:

```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

For **Windows (PowerShell)**:

```powershell
Start-Service ssh-agent
ssh-add ~\.ssh\id_rsa
```

✅ Your **SSH key is now active**!

---

## **📌 Step 4: Test SSH Connection**
Run:

```sh
ssh -T git@github.com
```

If successful, you’ll see:

```
Hi your-username! You've successfully authenticated, but GitHub does not provide shell access.
```

🔹 If you see `Permission denied (publickey)`, check that your key is **added to the agent** and **GitHub**.

---

## **📌 Step 5: Set SSH as Default for Git**
Tell Git to use SSH instead of HTTPS:

```sh
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
git remote set-url origin git@github.com:your-username/todo-actions.git
```

Now you can **push, pull, and clone** without passwords!

---

# **🖥️ Connect GitHub SSH to VS Code**
### **🔗 Method 1: Using Built-in Git Integration**
1. Open **VS Code** → **Command Palette** (`Ctrl+Shift+P` / `Cmd+Shift+P`)
2. Type **"Git: Clone"** and select **Git: Clone**
3. Enter your SSH repo URL:

   ```sh
   git@github.com:your-username/todo-actions.git
   ```

4. Select a folder and start coding!

---

### **🔗 Method 2: Using VS Code Terminal**
Open **VS Code Terminal** (`Ctrl+``) and run:

```sh
git clone git@github.com:your-username/todo-actions.git
cd todo-actions
code .
```

---

## **🔗 Connecting SSH from GitHub CLI**
If you use **GitHub CLI**, set SSH authentication:

```sh
gh auth login
```

1. Select **GitHub.com**
2. Choose **SSH Key**  
3. **Enter your SSH Key Path** (`~/.ssh/id_rsa`)  
4. ✅ Done! You are now authenticated.  

---

## **🔗 Automate SSH Key Setup (For Cloud Servers)**
For quick **SSH setup on remote machines**, run:

```sh
echo "your-ssh-key" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

This allows passwordless login for **GitHub CI/CD runners** or **cloud servers**.

---

## **✅ Summary**
🎯 **You successfully set up SSH authentication for GitHub!**  
💡 Now you can securely **push, pull, and automate workflows** without entering credentials.  

---

## **📜 Next Steps**
- **Set up multiple SSH keys** (`config` file in `~/.ssh/`).
- **Use SSH keys with GitHub Actions runners** for advanced CI/CD.
- **Integrate VS Code with remote servers** via SSH (`Remote-SSH` extension).  

---

---

# **🚀 Lab Instructions: To-Do List Automation with GitHub Actions**  

## **📌 Introduction**  
GitHub Actions is a **CI/CD automation tool** that helps automate workflows, manage repositories, and integrate external tools. In this lab, you will:
- **Create a GitHub Action workflow** to automate To-Do list management.
- **Use triggers** (`on: issues`) to automatically label and categorize tasks.
- **Implement notifications** for task updates.
- **Use secrets** to securely manage API keys for integrations.

This is a **practical DevOps exercise** to help you automate **task management using GitHub Actions**.

---

## **📖 Prerequisites**
Before starting, ensure you have:
1. **A GitHub Account** (Create one at [github.com](https://github.com))
2. **A GitHub Repository** (You can create a new one for this project)
3. **Basic Knowledge of Git & YAML**
4. **Slack Webhook URL** (for notifications)
5. **GitHub CLI (Optional, but recommended)**

---

## **📌 Step 1: Create a GitHub Repository**
Set up a new GitHub repository to store the To-Do workflow.  

```sh
git init
git remote add origin https://github.com/your-username/todo-actions.git
```

Clone the repository (if you haven't already):
```sh
git clone https://github.com/your-username/todo-actions.git
cd todo-actions
```

---

## **⚙️ Step 2: Define GitHub Action Workflow**
Create the **workflow file** at `.github/workflows/todo.yml`.

```yaml
name: "To-Do Manager"

on:
  issues:
    types: [opened, edited, closed]

jobs:
  label-tasks:
    runs-on: ubuntu-latest
    steps:
      - name: 📂 Checkout Repository
        uses: actions/checkout@v4

      - name: 🏷️ Label Task Based on Title
        uses: actions-ecosystem/action-add-labels@v1
        with:
          labels: |
            high-priority: 'priority: high'
            medium-priority: 'priority: medium'
            low-priority: 'priority: low'

  notify-on-close:
    runs-on: ubuntu-latest
    if: github.event.action == 'closed'
    steps:
      - name: 📩 Send Notification on Task Completion
        uses: rtCamp/action-slack-notify@v2
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
          SLACK_MESSAGE: "✅ Task '${{ github.event.issue.title }}' has been completed!"

```

---

## **📌 Step 3: Set Up GitHub Secrets**
1. **Go to your GitHub Repository** → `Settings` → `Secrets and Variables` → `Actions`
2. **Add a new secret**:
   - **Name:** `SLACK_WEBHOOK`
   - **Value:** Your Slack Webhook URL

Secrets allow you to **securely store API keys, passwords, and tokens** without exposing them in code.

---

## **📌 Step 4: Commit & Push Your Workflow**
Save your workflow file and push it to GitHub.

```sh
git add .github/workflows/todo.yml
git commit -m "Added To-Do GitHub Action workflow"
git push origin main
```

---

## **🔎 Step 5: Understanding Workflow Execution**
### **What Happens When You Create an Issue?**
1. A **GitHub Issue** is created as a **To-Do task**.
2. The **workflow triggers automatically** and:
   - Assigns a **priority label** based on the issue title.
   - Adds the issue to the **To-Do project board** (if enabled).
3. If the issue is **closed**, a **Slack notification** is sent.

---

## **📌 Step 6: Advanced Features & Enhancements**
### **1️⃣ Auto-Assign Issues**
Assign issues automatically to a specific team member using:

```yaml
- name: 🤖 Auto-Assign Issue
  uses: pozil/auto-assign-issue@v1
  with:
    assignees: "your-username"
```

### **2️⃣ Generate To-Do Report (Using Artifacts)**
Save completed tasks into a `completed_tasks.md` file:

```yaml
- name: 📝 Save Completed Tasks
  run: echo "Task '${{ github.event.issue.title }}' completed on $(date)" >> completed_tasks.md
- name: 📦 Upload Report as Artifact
  uses: actions/upload-artifact@v3
  with:
    name: To-Do Report
    path: completed_tasks.md
```

### **3️⃣ Close Inactive Issues Automatically**
Use **GitHub's stale bot** to close issues that are inactive:

```yaml
- name: ⏳ Close Stale Issues
  uses: actions/stale@v8
  with:
    days-before-stale: 30
    days-before-close: 7
    stale-issue-message: "This issue has been inactive for 30 days. Marking as stale."
    close-issue-message: "This issue was closed due to inactivity."
```

---

## **📜 Theory & Key Concepts**
### **GitHub Actions Basics**
- **Workflows (`.github/workflows/`)**: Define automated tasks.
- **Triggers (`on:` keyword)**: Events that start the workflow (e.g., `push`, `issues`).
- **Jobs**: A sequence of tasks executed in the workflow.
- **Steps**: Individual actions in a job.

### **Security Best Practices**
✅ **Use Secrets** instead of hardcoding credentials.  
✅ **Restrict Workflow Permissions** to limit access to repository data.  
✅ **Monitor Logs & Debug Errors** using GitHub Actions' built-in logs.

---

## **📜 Lab Summary**
✅ **You created an automated GitHub Action to manage a To-Do List**.  
✅ **You used triggers, labels, notifications, and artifacts**.  
✅ **You learned how to secure API keys using GitHub Secrets**.  
✅ **You explored advanced enhancements for better automation**.  

---

## **📜 Next Steps**
- **Extend This Project**:
  - Add **Jira/Trello integration** for project management.
  - Use **GitHub API** to fetch task details dynamically.
  - Automate **email notifications** for issue updates.

- **Explore GitHub Actions Further**:
  - Read **[GitHub Actions Documentation](https://docs.github.com/en/actions)**
  - Experiment with **self-hosted runners** for customized workflows.
  - Learn **Advanced CI/CD Pipelines** with GitHub Actions.

---

## **📜 License**
[MIT License](LICENSE)

---

## **🔗 Resources**
📖 **GitHub Actions Docs**: [GitHub Docs](https://docs.github.com/en/actions)  
📌 **Slack Webhook Setup**: [Slack API](https://api.slack.com/messaging/webhooks)  
📌 **GitHub Secrets Guide**: [Managing Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)  

---



---
## **🚀 Ready to Automate & Secure Your GitHub?**
💡 **Would you like me to create this README file in your repository?** If yes, share the **repository URL** and **branch name**! 🚀




