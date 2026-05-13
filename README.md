# hikaligroupshare

A tiny browser-side encryption page for sharing AI-generated anime art with a small circle of friends.

**Live:** https://slimpigs.github.io/hikaligroupshare/

For learning and fun only. Fun encryption, so hopefully your dad won't notice it.

---

## What this is

A single self-contained HTML page (Sky Striker // Roze relay) that encrypts any file in your browser with **AES-256-GCM** and a **6-word passphrase**, producing a `.ssae.txt` ciphertext you can host anywhere — raw GitHub, gist, pastebin, any direct-link host — and share with people who know the words. The recipient pastes the URL or drops the file into the same page, types the same 6 words, and gets the original file back.

Nothing leaves your browser tab during encryption or decryption. The crypto runs entirely client-side. The friend who sends you the ciphertext never sees your password, and the server hosting the ciphertext never sees your file.

## Content note

The artwork shared through this tool is **AI-generated anime art**. Some pieces may contain **NSFW** elements, but everything is illustrated / drawn-style and is intended only for the friend group it was made for. Please do not redistribute outside that group.

## How to use

### Sealing a file (sender)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. Stay on the **SEAL** tab (Loadout 01).
3. Drop an image / video / any file onto the panel, or click to pick one. Max ~80 MB.
4. Fill in your **6 cipher words** in the lock grid. This is the passphrase — share it with the recipient through a **different channel** from the ciphertext.
5. Click **Engage Seal**. After a moment the sealed transmission appears.
6. Click **Save .ssae.txt** to write it to disk, or **Copy String** to grab it as text. Then host the file somewhere (a GitHub gist, this repo, a paste site, any direct-link host) and send the link.

### Breaching a file (recipient)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. Switch to the **BREACH** tab (Loadout 02).
3. Choose one:
   - Paste the URL of the `.ssae.txt` into the URL field and click **Fetch**, **or**
   - Paste the ciphertext directly into the input box, **or**
   - Drop the downloaded `.ssae.txt` onto the panel.
4. Type the same **6 cipher words** the sender gave you. Order matters.
5. Click **Breach Transmission**. The recovered file previews in the panel; click **Download Recovered File** to save it locally.

## Technical details

- **Cipher:** AES-256-GCM (authenticated encryption — tampering is detected)
- **Key derivation:** PBKDF2 / SHA-256 / 310,000 iterations
- **Salt / IV:** randomly generated per encryption (16 / 12 bytes)
- **Packet format:** `MAGIC(4) | VERSION(1) | SALT(16) | IV(12) | CIPHERTEXT`, base64-encoded
- **Zero network during crypto.** No server calls during encrypt or decrypt. Your words never leave your browser.

## Please read before using

- **There is no password recovery.** Lose the 6 words and the file is gone forever. Write them down.
- **Send the words and the ciphertext through different channels.** If both land in the same chat, the encryption protects you from almost nothing.
- **Do not use this tool for any illegal purpose.** It exists for sharing personal AI-generated anime art among a small private group. Using it to transmit illegal content, harass anyone, distribute material that violates someone's rights, or break local laws is against the spirit of this project. Any such use is entirely the user's responsibility — not the author's.
- Max file size is around 80 MB (browser memory limit).

---

# 哈卡利共享 (中文)

一个浏览器端的小工具，用来在小范围的朋友圈里分享我用 AI 制作的二次元插画。

**在线地址：** https://slimpigs.github.io/hikaligroupshare/

仅供学习和玩耍。趣味加密，希望你爸不会发现。

---

## 这是什么

一个独立的单页 HTML（Sky Striker // Roze 中继器），在你的浏览器里用 **AES-256-GCM** 加密任何文件，密码是 **6 个单词**，输出一个 `.ssae.txt` 密文文本。你可以把这个文件丢到任何地方 —— GitHub raw、gist、paste 站、网盘直链都行 —— 然后通过另一个渠道把 6 个单词告诉对方。对方在同一个页面粘贴链接或拖入文件、输入相同的 6 个单词，就能还原原始文件。

整个加解密过程都在浏览器里完成，密码不会离开你的标签页。给你发密文的朋友看不到你的密码，托管密文的服务器也看不到你的原文件。

## 内容说明

通过这个工具分享的图片是 **AI 生成的二次元插画**。部分内容可能包含 **NSFW**（不适合工作场所观看）元素，但全部都是动漫风格的绘画，仅在本朋友圈内部欣赏，请不要向外传播。

## 使用方法

### 加密（发送方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. 停留在 **SEAL（封印）** 标签页（Loadout 01）。
3. 拖入一张图片 / 视频 / 任意文件，或点击面板选择。最大约 80 MB。
4. 在密码格中填入你的 **6 个密码词**。这就是密钥 —— 请通过和密文**不同的渠道**告诉接收方。
5. 点击 **Engage Seal**。稍等片刻，加密后的字符串会出现。
6. 点击 **Save .ssae.txt** 保存到本地，或 **Copy String** 复制为文本。然后把文件丢到任何地方（GitHub gist、本仓库、网盘、paste 站等），把直链发给对方。

### 解密（接收方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. 切到 **BREACH（破封）** 标签页（Loadout 02）。
3. 三选一：
   - 把 `.ssae.txt` 的链接粘到 URL 框，点 **Fetch**；
   - 直接把密文粘进输入框；
   - 把下载好的 `.ssae.txt` 拖到面板上。
4. 填入发送方给你的 **6 个密码词**（顺序要一致）。
5. 点击 **Breach Transmission**。还原后的文件会在下方预览，点 **Download Recovered File** 保存到本地。

## 技术细节

- **加密算法**：AES-256-GCM（带完整性校验，篡改会被发现）
- **密钥推导**：PBKDF2 / SHA-256 / 31 万次迭代
- **Salt 与 IV**：每次加密随机生成（16 / 12 字节）
- **数据包格式**：`MAGIC(4) | VERSION(1) | SALT(16) | IV(12) | 密文`，整体 base64 编码
- **加解密过程零网络**。没有任何服务器请求，密码词不会离开你的浏览器。

## 重要提示 — 请认真阅读

- **没有"忘记密码"功能**。如果 6 个密码词丢了，文件就再也打不开。请记好。
- **密码词和密文请用不同的渠道传**。两样东西放同一个聊天里发出去，等于没加密。
- **请勿用于任何非法用途**。本工具只是为了在一个小范围的私人朋友圈里分享 AI 生成的二次元插画。如果有人用它来传播违法内容、骚扰他人、侵犯他人权利、或者违反当地法律，责任完全在使用者本人，与作者无关。
- 单个文件大小上限约 80 MB（受浏览器内存限制）。
