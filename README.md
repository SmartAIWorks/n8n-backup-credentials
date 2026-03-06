# 🔐 n8n Credentials Backup to Google Drive

A secure **n8n automation workflow** that exports all credentials from your n8n instance and backs them up to **Google Drive** as individual JSON files.

Each execution creates a **timestamped backup folder**, ensuring your credentials are **organized, versioned, and recoverable**.

⚠️ This workflow exports **decrypted credentials**, making it extremely useful for **migration and disaster recovery**, but it must be handled carefully for security reasons.

---

# ✨ Features

- 🔐 Exports **all n8n credentials**
- ☁️ Uploads backups automatically to **Google Drive**
- 📁 Creates a **timestamped backup folder**
- 📄 Saves each credential as a **separate JSON file**
- 🔁 Uses batch processing to safely iterate through credentials
- 🧾 Maintains **clear credential version history**

---

# 🧠 How It Works

The workflow performs the following steps:

1️⃣ Manually trigger the workflow  
2️⃣ Create a **timestamped Google Drive folder**  
3️⃣ Execute the n8n CLI command to **export credentials**  
4️⃣ Parse the exported credential data  
5️⃣ Split credentials into individual items  
6️⃣ Convert each credential into a **JSON file**  
7️⃣ Upload the files to the backup folder in Google Drive  

---

# 🏗 Workflow Architecture

```
Manual Trigger
      │
      ▼
Create Backup Folder (Google Drive)
      │
      ▼
Execute n8n CLI Command
      │
      ▼
Parse Credential Output
      │
      ▼
Split Credentials
      │
      ▼
Loop Through Credentials
      │
      ▼
Convert Credential → JSON File
      │
      ▼
Upload File to Google Drive
      │
      └── Repeat for every credential
```

---

# 📂 Google Drive Backup Structure

Each workflow execution creates a folder like:

```
n8n_creds/
   └── credentials_2026-03-06_143025/
         ├── Google Drive.json
         ├── Telegram Bot.json
         ├── Slack API.json
```

Folder naming format:

```
credentials_yyyy-MM-dd_HHmmss
```

Example:

```
credentials_2026-03-06_143025
```

---

# 🔧 Workflow Nodes Explained

## 🟢 Manual Trigger

Starts the workflow manually inside the **n8n editor**.

Useful for:

- On-demand backups
- Testing the workflow

---

## 📁 Create Folder (Google Drive)

Creates a new folder inside the **n8n_creds** directory.

Dynamic folder name:

```
credentials_{{ $now.format('yyyy-MM-dd_HHmmss') }}
```

---

## ⚙️ Execute Command

Runs the **n8n CLI command** to export credentials.

```
n8n export:credentials --all --decrypted
```

This command returns all credentials in **JSON format**.

⚠️ The `--decrypted` flag exports credentials in **plain text**.

---

## 📝 Edit Fields

Extracts the credential data from the command output.

```
$input.first().json.stdout
```

This stores the credentials in a field called:

```
creds
```

---

## 🔀 Split Out

Splits the credential array into **individual items** so they can be processed one at a time.

---

## 🔁 Loop Over Items

Uses **Split In Batches** to process credentials sequentially.

This prevents memory overload and keeps execution stable.

---

## 📄 Convert to File

Converts each credential JSON object into a file.

Dynamic filename:

```
{{$json.name}}.json
```

Example output:

```
Google Drive.json
Slack.json
Telegram.json
```

---

## ☁️ Upload File

Uploads the JSON file to the **timestamped Google Drive folder**.

Folder reference:

```
$('Create folder').item.json.id
```

---

# 🔑 Requirements

Before running the workflow, configure the following.

---

## 1️⃣ Google Drive OAuth2 Credential

Required for:

- Create Folder
- Upload File

Required scopes:

```
drive
drive.file
```

---

## 2️⃣ n8n CLI Access

The workflow uses the **Execute Command node**, which requires:

- n8n running in an environment where CLI access is allowed
- Permissions to execute:

```
n8n export:credentials
```

---

# ⚙️ Setup Guide

## 1️⃣ Import the Workflow

Import the workflow JSON into your **n8n instance**.

---

## 2️⃣ Configure Google Drive

Select your parent folder:

```
n8n_creds
```

---

## 3️⃣ Ensure CLI Access

Your n8n instance must allow execution of:

```
n8n export:credentials --all --decrypted
```

This typically works when running:

- Self-hosted n8n
- Docker-based n8n
- VPS installations

---

## 4️⃣ Execute the Workflow

Click:

```
Execute Workflow
```

The workflow will:

1️⃣ Export credentials  
2️⃣ Create a backup folder  
3️⃣ Upload each credential as a JSON file  

---

# 🔐 Security Considerations

⚠️ **Important**

This workflow exports **decrypted credentials**.

The backup files may contain sensitive information such as:

- API keys
- OAuth tokens
- Database credentials
- Service secrets

---
