# WizardTrial
## Challenge
- **Solves**: 44
- **Name**: WizardTrial
- **Points**: 100

## Description
Velkommen lærling til hackingverdenen. Din første opgave er at lære den oplåsende charme. Du har fået en liste over mulige adgangskoder, men den store troldmand har beskyttet flaget med en mystisk hash $\mathbf{0}$. Held og lykke, unge troldmand!  

**Command**: `nc wizard-trial.hkn 1337`  
**Zip SHA256**: `1247BFAC8FE09A2A8F0A7940195BA23E59FB67FC942CCD8C68AE2550E8695716`

**NOTE**: Create a user and find the VPN and Browser LABs on Campfire Labs

## Solution
Connecting to the server:  
```bash
nc wizard-trial.hkn 1337
h*1D
h*336db7f20000
h**ENTER PASSWORD:
```

The zip file contains a wordlist and a magic function that:  
- Prints a hash of a secret password.  
- Asks for input, hashes it using `magic()`, and compares it to the original hash.  
- Reveals the flag if it matches.  

I replicated the magic function locally:  
```python
def magic(input_str):
    k = 57 - 30 + 3 + 1  # 31
    l = 222 // 6         # 37
    for c in input_str:
        j = (ord(c) + k) * l
        j = j % 1_000_000_007
    j = ((j << 10) | (j >> 30)) & 0xffffffffff  # 40-bit rotate left by 10
    return format(j, 'x')

with open("wordlist.txt", "r") as f:
    for line in f:
        pwd = line.strip()
        if magic(pwd) == "336db7f20000":
            print(f"[+] Password found: {pwd}")
            break
```

Output: `sh4d0w`  

Then:  
```bash
nc wizard-trial.hkn 1337
ENTER PASSWORD: sh4d0w
```

## Flag
`DDC{sh4d0w_w1z4rd}`