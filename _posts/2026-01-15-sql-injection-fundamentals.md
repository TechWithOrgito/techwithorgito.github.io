---
layout: post
title: "Understanding SQL Injection Attacks"
date: 2026-01-15
---

SQL injection remains one of the most common and dangerous web application vulnerabilities. This post explains the fundamentals.

## The Vulnerability

SQL injection occurs when user input is directly concatenated into SQL queries without proper sanitization:

```python
# Vulnerable code
username = request.GET['username']
query = f"SELECT * FROM users WHERE username = '{username}'"
cursor.execute(query)
```

An attacker can input `admin' OR '1'='1` to bypass authentication.

## Exploitation Technique

Basic SQL injection follows this pattern:

```sql
-- Original query
SELECT * FROM users WHERE username = 'admin' AND password = 'pass123'

-- Injected payload
username: admin' OR '1'='1' --
password: anything

-- Resulting query
SELECT * FROM users WHERE username = 'admin' OR '1'='1' --' AND password = 'anything'
```

The `--` comment operator causes everything after it to be ignored.

## Prevention

Use parameterized queries:

```python
# Secure code
username = request.GET['username']
query = "SELECT * FROM users WHERE username = ?"
cursor.execute(query, (username,))
```

Additional defenses:

- Input validation and sanitization
- Least privilege database accounts
- Web application firewalls
- Regular security audits

## Detection

Look for these indicators in logs:

```text
username=admin' OR '1'='1
id=1 UNION SELECT null,null,null--
search=' AND 1=CONVERT(int, (SELECT @@version))--
```

Tools like SQLMap automate detection and exploitation during security assessments.

## Real-World Impact

SQL injection can lead to:

- Authentication bypass
- Data exfiltration
- Database modification or deletion
- Remote code execution (in some configurations)

Always validate input and use parameterized queries.