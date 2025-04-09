# Sysmon Spectacle
## Challenge
- **Solves**: 18
- **Name**: sysmon-spectacle
- **Points**: 127

## Description
Der er opstået en sikkerhedshændelse på en af Sagalabs' arbejdsstationer. En af administratorerne blev overrasket over at finde en mærkelig eksekverbar fil på serveren, og værten genererede usædvanlig trafik til en fremmed IP-adresse. SagaLabs' sikkerhedsteam har formået at indsamle nogle Sysmon-logs, som er blevet genereret på maskinen, men de skal bruge din hjælp til at fortolke dem og afdække detaljerne omkring hændelsen.

**Note**: Flaget kan findes lokalt fra det log man får ud.

## Download
- **SHA256**: A3A23AE5DCECFC259F23C488F450FC6F841BA3C3026B56CF812B70AC29B499F5

## Solution
We are given a log file. I tried searching for "RERD" (base64 for "ddc"). One log had this string:  
`RERDezVZNU0WTjE1NFczNTBNM1dVSFV9`  
Which decrypted to:  
`DDC{5Y5MON154W350M3WUHU}`

## Flag
`DDC{5Y5MON154W350M3WUHU}`