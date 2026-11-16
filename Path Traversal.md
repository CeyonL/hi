# Path Traversal

## Contents

1. [Unzip Me Now](#1-unzip-me-now)
2. [CVE-2021-43798](#2-cve-2021-43798)
3. [Directory traversal](#3-directory-traversal)

## 1. Unzip Me Now

**Challenge:** [CookieArena — Unzip Me Now](https://battle.cookiearena.org/challenges/web/unzip-me-now)

Tried uploading a ZIP file here:

![ZIP upload](wu-markdown-assets/bai3-path-traversal/image24.png)

After uploading, it redirected me to `/list_extracted`:

![Extracted folders](wu-markdown-assets/bai3-path-traversal/image25.png)

Clicked an extracted folder, and the link changed to `/view_folder/`:

![Folder contents](wu-markdown-assets/bai3-path-traversal/image26.png)

Then I checked the challenge hint:

![Challenge hint](wu-markdown-assets/bai3-path-traversal/image27.png)

I looked into how ZIP files handle symlinks, and of course Stack Overflow helped me once again. Such a lifesaver.

![Stack Overflow answer](wu-markdown-assets/bai3-path-traversal/image28.png)

So I made a symlink to `/flag.txt` and zipped it with `--symlinks`:

![Creating the ZIP](wu-markdown-assets/bai3-path-traversal/image29.png)

The final flag:

```text
CHH{zIPpy_PATH_tRAvERS4l_efabd35dc358}
```

## 2. CVE-2021-43798

**Challenge:** [CookieArena — CVE-2021-43798](https://battle.cookiearena.org/challenges/cve/cve-2021-43798)

Okay, this one was interesting. With help from the first hint, I found the Grafana version:

![Challenge hint](wu-markdown-assets/bai3-path-traversal/image21.png)

![Grafana 8.3.0](wu-markdown-assets/bai3-path-traversal/image22.png)

Searched the app name and version on Google and found an exploit for this CVE. It used a traversal path under `/public/plugins/`.

Tried it directly in the browser by changing the URL, but it just kept sending me back to `/login`. Then I opened Burp Suite, sent the same path there, and got the flag:

![Burp request and flag](wu-markdown-assets/bai3-path-traversal/image23.png)

## 3. Directory traversal

**Challenge:** [Root-Me — Directory traversal](https://www.root-me.org/en/Challenges/Web-Server/Directory-traversal)

The first page:

![Photo gallery](wu-markdown-assets/bai3-path-traversal/image30.png)

Read the description:

![Challenge description](wu-markdown-assets/bai3-path-traversal/image31.png)

Tried `../`, and it returned two entries:

![Two entries](wu-markdown-assets/bai3-path-traversal/image32.png)

Tried opening `galerie` directly, but got forbidden:

![403 Forbidden](wu-markdown-assets/bai3-path-traversal/image33.png)

Tried going up further with more `../` segments, but got an `open_basedir` warning. Guess I'd gone outside the allowed directory?

![open_basedir warning](wu-markdown-assets/bai3-path-traversal/image34.png)

Tried `./` to see the directory I was already in:

![Current directory](wu-markdown-assets/bai3-path-traversal/image35.png)

Found a suspicious folder. Opened it directly and got forbidden again:

![403 Forbidden again](wu-markdown-assets/bai3-path-traversal/image36.png)

Opened it through `ch15.php` instead:

![Hidden folder](wu-markdown-assets/bai3-path-traversal/image37.png)

Found the password file there and got the challenge password:

![Challenge password](wu-markdown-assets/bai3-path-traversal/image38.png)
