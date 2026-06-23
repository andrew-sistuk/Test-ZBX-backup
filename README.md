# Zabbix Asset & Template Backup

## Overview

This application exports Zabbix configuration objects (templates, hosts, maps, media types, host groups, template groups, images, etc.) and stores them in a Git repository for version control and backup purposes.

---

## Prerequisites

Before installation, ensure the following software is installed:

- Python 3.11+
- Git
- Access to a Zabbix server
- Access to the target Git repository

---

## Configure Zabbix User Role

### Create User Role

Navigate to:

`Users → User roles → Create user role`

Create a new role:

**Name:** `C_FZ_API_ZBX_BACKUP`

Configure the role with the following settings:

| Setting | Value |
|----------|----------|
| User type | Super Admin |
| Access to UI elements | Dashboards |
| Default access to new UI elements | NO |
| Read-write access to services | None |
| Read-only access to services | None |
| Access to modules | NO |
| Default access to new modules | NO |
| Access to actions | NO |
| Default access to new actions | NO |

### API Permissions

Allow access to the following API methods:

```text
configuration.export
image.get
hostgroup.get
host.get
map.get
mediatype.get
templategroup.get
template.get
```

---

## Create API User

Navigate to:

`Users → Users → Create user`

Create a dedicated service account for backups.

Assign:

- User role: `C_FZ_API_ZBX_BACKUP`
- Required User Groups

Save the user.

---

## Create API Token

Navigate to:

`Users → API Tokens → Create API token`

Generate a new API token and save it securely.

---

## Clone Repository

```bash
git clone <repository_url>
cd <repository_name>
```

---

## Create Python Virtual Environment

Create a virtual environment:

```bash
python3 -m venv .venv
```

Activate it.

### Linux / macOS

```bash
source .venv/bin/activate
```

### Windows

```powershell
.venv\Scripts\activate
```

Verify activation:

```bash
which python
```

or

```powershell
where python
```

The executable should point to the `.venv` directory.

---

## Install Dependencies

Upgrade pip:

```bash
pip install --upgrade pip
```

Install required packages:

```bash
pip install -r requirements.txt
```

---

## Configure Environment Variables

Create the environment file:

```bash
cp .env.ex .env
```

Edit `.env` and configure the required values:

```env
ZABBIX_URL=https://zabbix.example.com
ZABBIX_TOKEN=<generated_api_token>

GIT_REPO=git@github.com:organization/repository.git
```

Replace the example values with your environment settings.

---

## Configure Git SSH Access

Verify SSH connectivity:

```bash
ssh -T git@github.com
```

Expected output:

```text
Hi <username>! You've successfully authenticated.
```

If SSH authentication is not configured, create and register an SSH key before continuing.

---

## Run Application

```bash
python main.py
```

---

## Verify Export

After successful execution:

1. Zabbix objects will be exported.
2. Files will be generated locally.
3. Changes will be committed automatically.
4. Changes will be pushed to the configured Git repository.

Verify that a new commit appears in the target repository.

---

## Troubleshooting

### GitHub SSH Authentication Failed

```text
Permission denied (publickey)
```

Verify SSH access:

```bash
ssh -T git@github.com
```

### No Changes to Commit

```text
No changes to commit
```

The exported configuration matches the latest version already stored in Git.

### Invalid API Token

Verify:

- API token value in `.env`
- Assigned role permissions
- User group membership
- Zabbix API accessibility
