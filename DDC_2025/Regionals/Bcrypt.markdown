# Bcrypt (I got junior firstblood)
## Challenge
- **Solves**: 21
- **Name**: bcrypt
- **Points**: 100

## Description
Hashing af adgangskoder er klart at sikre. Jeg læste på nettet, at det var en god idé at hashe ikke kun adgangskoden, men også brugernavnet for at binde de to sammen. Jeg læste også, at funktionen `bcrypt()` er en fantastisk løsning til hashing med adgangskoder.  

Jeg installerede et system ved at bruge denne `/etc/passwd`, men det er mærkeligt, at nogle brugernavne er ret lange. Ville det være et problem på en eller anden måde?  

**Crypto-bcrypt SHA256**:  
`4511970E267723D6FF1E058CD954,onD97E301AE7395D3450C9AF0D6529D1D477`  

**Command**: `nc bcrypt.hkn 1337`

**NOTE**: Create a user and find the VPN and Browser LABs on Campfire Labs

## Solution
The admin username is exactly 71 bytes, which makes only the first character of the password important for logging in.  

Password solve script:  
```python
import bcrypt
import string

admin_username = "admin@llanfairpwllgwyngyllgogerychwyrndrobwllllantysiliogogogoch.co.uk"
stored_hash = "$2b$12$Pfcn.zxBDtcxsACtvJBHGelpEDVxKxR.BvJANB9xVvEP18bGAk.I."

candidates = string.ascii_letters + string.digits + "-."
for c in candidates:
    test_input = (admin_username + ":" + c).encode('utf-8')
    if bcrypt.checkpw(test_input, stored_hash.encode('utf-8')):
        print("Found candidate password character:", c)
```

The script found that `M` was the password.  

I logged in with:  
```bash
nc bcrypt.hkn 1337
server login: admin@llanfairpwllgwyngyllgogerychwyrndrobwllllantysiliogogogoch.co.uk
server password: M
```

## Flag
`DDC{truncating_input_in_password_hashing_is_nuts}`