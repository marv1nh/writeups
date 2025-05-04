#  Bøf-baserede værdipapirer og emails

**Category:** Forensics  
**Points:** 557  
**Solves:** 8  
**Author:** *marv1nh*
**Junior first blood**
---

## Description

> I en yderst vigtig e-mail korrespondance diskuterer JD Vance og Donald Trump forretningsidéer i topklasse, store hemmeligheder og absolut ikke mistænkelige aftaler.  
> 
> Dog har de, enten på grund af manglende cybersikkerhedsprincipper eller en lidt for afslappet tilgang til dem, efterladt et flag i en af deres e-mails.
>
> Uheldigt for dem (men heldigt for vores Nationals-deltagere) blev en kopi af denne udveksling opsnappet via en ikke-offentliggjort XKEYSCORE-node.  
> En tidligere NSA-medarbejder, der netop var blevet fyret af Elon Musks DOGE-projekt, har lækket et disk-image fra denne XKEYSCORE-node — og han så bestemt ikke tilfreds ud.

---

##  Challenge Details

-  **Handout:** A disk image
-  **Objective:** Recover the hidden flag from the disk image, hinted to be in an email

---

## 🔍 Solution

### 1. Mount & Analyze the Disk Image

I started by opening the disk image using **Autopsy**, a digital forensics platform.

Once loaded, I navigated to the **Emails** section to explore the email correspondence stored on the disk.

---

### 2. Discovering Deleted Files

While browsing the contents, I noticed some **deleted files**, which often hold valuable hidden information.

Among them was a **deleted `.pcap` file**, which immediately drew my attention. A pcap (packet capture) could contain intercepted communications—very promising in the context of this challenge.

---

### 3. Inspecting the PCAP

I exported the `.pcap` file from Autopsy and opened it in **Wireshark**

I examined the TCP streams and noticed something unusual in **TCP Stream 4**:

```
tcp.stream eq 4
```

This stream contained a large blob of **Base64-encoded** data, and the provided message from this and earlier mails suggest that it contains the flag.

---

### 4. Extracting and Decoding the Payload

I copied the Base64 content and saved it into a file:

```bash
echo "base64 here" > file.txt
```

Then, I decoded it into an image using:

```bash
cat file.txt | base64 -d > output.jpeg
```

---

### 5. Viewing the Flag

Upon opening the resulting `output.jpeg`, the flag was **clearly visible in the image** 

---

##  Flag

*DDC{HUGE_VERY_BIG_COIN}*

---

##  Tools Used

- Autopsy
- Wireshark
- Base64 decoding
- Linux CLI tools (cat, base64)
