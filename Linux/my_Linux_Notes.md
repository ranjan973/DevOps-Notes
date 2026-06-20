# Linux Administration Quick Notes

## APT Package Management

### Search Available Packages

Search the APT repository when you don't know the exact package name:

```bash
apt search openjdk
```

---

### Remove an Installed Package

```bash
apt remove jenkins
```

Removes the Jenkins package but may leave configuration files behind.

---

### List Installed Packages

Show installed JDK packages:

```bash
sudo apt list --installed *jdk*
```

---

# Firewall Management (UFW)

## Allow a Port

Allow TCP port 8080 through the firewall:

```bash
sudo ufw allow 8080
```

### UFW

**UFW (Uncomplicated Firewall)** is a simplified interface for managing Linux firewall rules.

---

# Docker User & Group Management

## List Users in the Docker Group

```bash
getent group docker
```

Displays the Docker group and its members.

Example:

```text
docker:x:999:user1,user2,user3
```

---

## Create a User

```bash
sudo adduser dockeradmin
```

Benefits of `adduser`:

- Creates user account
- Creates home directory automatically
- Prompts for password
- Creates user-friendly defaults

Example home directory:

```text
/home/dockeradmin
```

---

## Add User to Docker Group

```bash
sudo usermod -aG docker dockeradmin
```

### Breakdown

```text
-a  → Append
-G  → Secondary Group
```

This allows the user to run Docker commands without using `sudo`.

Verify membership:

```bash
groups dockeradmin
```

---

# useradd vs adduser

## useradd

Low-level command for user creation.

Example:

```bash
sudo useradd username
```

Characteristics:

- Does not create a home directory by default
- Does not prompt for password
- Requires more manual configuration

---

## adduser

Higher-level, user-friendly wrapper around `useradd`.

Example:

```bash
sudo adduser username
```

Characteristics:

- Creates home directory automatically
- Prompts for password
- Creates sensible defaults
- Preferred on Debian/Ubuntu systems

---

# Service Accounts

## Create a User with a Non-Interactive Shell

```bash
sudo useradd -s /sbin/nologin siva
```

### Purpose

Creates a user that cannot log in interactively.

Commonly used for:

- Services
- Daemons
- Application accounts

Examples:

```text
jenkins
nginx
mysql
postgres
```

### Why Use Non-Interactive Shells?

Improves security by preventing direct login while still allowing the account to own files and run services.

Verify:

```bash
grep siva /etc/passwd
```

Example output:

```text
siva:x:1002:1002::/home/siva:/sbin/nologin
```

---

# Quick Revision

### Package Management

```bash
apt search openjdk
apt remove jenkins
sudo apt list --installed *jdk*
```

### Firewall

```bash
sudo ufw allow 8080
```

### Docker Group

```bash
getent group docker
sudo usermod -aG docker dockeradmin
```

### User Management

```bash
sudo adduser dockeradmin
sudo useradd username
```

### Service Account

```bash
sudo useradd -s /sbin/nologin siva
```
