# hikaligroupshare

A tiny browser-side encryption page for sharing AI-generated anime art with a small circle of friends.

**Live:** https://slimpigs.github.io/hikaligroupshare/

For learning and fun only. Fun encryption, so hopefully your dad won't notice it.

---

## What this is

A single self-contained HTML page (Sky Striker // Roze relay) that encrypts any file in your browser with **AES-256-GCM** and a **6-word passphrase**, producing a `.ssae.txt` ciphertext you can host anywhere — raw GitHub, gist, pastebin, any direct-link host — and share with people who know the words. The recipient pastes the URL or drops the file into the same page, types the same 6 words, and gets the original file back.

Nothing leaves your browser tab during encryption or decryption. The crypto runs entirely client-side. The friend who sends you the ciphertext never sees your password, and the server hosting the ciphertext never sees your file.

## Content policy

**Going forward, this site stores only SFW content.** The tool itself is general-purpose encryption — anyone can use it to seal any file locally on their own machine — but the `.ssae.txt` ciphertexts hosted in *this* repo will only ever be AI-generated **safe-for-work** anime artwork from now on. Earlier ciphertexts that may have contained NSFW material have been purged from the repo and its git history.

Either way, nothing shared through this tool should be redistributed outside the friend group it was made for.

## Ace modes (NEW)

At the top of the page there's an **ACE SELECT** rail with six Sky Striker Ace identities:

| Mode | Pilot | Arms | Energy color |
|---|---|---|---|
| **Raye** | Raye | base | sky-blue |
| **Kagari** | Raye | Kagari armor | fire-orange |
| **Shizuku** | Raye | Shizuku armor | electric cyan |
| **Hayate** | Raye | Hayate armor | emerald green |
| **Roze** | Roze | base | magenta-rose |
| **Zeke** | Roze | Zeke armor | dark crimson + gold |

Each mode has its own **distinct encryption pepper**. This means **the ace mode is a required second factor** alongside the 6 cipher words — a `.ssae.txt` sealed under Kagari mode can ONLY be breached when the recipient is in Kagari mode. Wrong mode = decryption fails with a clear error. There is no fallback.

So when you share a sealed file, tell the recipient *which mode you sealed it in*, the same way you tell them the 6 cipher words. (Sender and recipient should both click the same chip in the ACE SELECT rail before sealing / breaching.)

## How to use

### Sealing a file (sender)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. **Pick your ace mode** in the ACE SELECT rail at the top. Remember it — you'll tell the recipient.
3. Stay on the **SEAL** tab (Loadout 01).
4. Drop image / video / any file(s) onto the panel, or click to pick. Multiple files OK, they seal in parallel. Max ~80 MB each.
5. Fill in your **6 cipher words** in the lock grid. This is the passphrase — share it with the recipient through a **different channel** from the ciphertext.
6. Click **Engage Seal**. After a moment the sealed transmissions appear, one row per file.
7. Click **Save** on each row to write `.ssae.txt` to disk, or **Save All** to download all. **Copy** copies a single cipher to clipboard; **Copy All as JSON** bundles them all as a single chat-friendly blob. Then host the file(s) and send the link(s).
8. **Tell the recipient three things:** the 6 cipher words, the ace mode you sealed in, and where to fetch the `.ssae.txt`.

### Breaching a file (recipient)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. **Set the ACE SELECT to the same mode the sender used.** This is required — wrong mode will fail to decrypt.
3. Switch to the **BREACH** tab (Loadout 02).
4. Choose one:
   - Paste the URL of the `.ssae.txt` into the URL field and click **Fetch**, **or**
   - Paste the ciphertext directly into the input box, **or**
   - Drop the downloaded `.ssae.txt` onto the panel.
5. Type the same **6 cipher words** the sender gave you. Order matters.
6. Click **Breach Transmission**. The recovered file previews in the panel; click **Download Recovered File** to save it locally.

If breach fails with *"sealed under a different ace mode"*, switch the ACE SELECT to the mode the sender used and try again.

## Technical details

- **Cipher:** AES-256-GCM (authenticated encryption — tampering is detected)
- **Key derivation:** PBKDF2 / SHA-256 / 310,000 iterations
- **Salt / IV:** randomly generated per encryption (16 / 12 bytes)
- **Per-mode pepper:** each ace mode has its own secret pepper baked into the page and appended to the password before PBKDF2. Six modes → six separate encryption namespaces. The mode therefore acts as a second factor.
- **Parallel sealing:** multi-file batches derive the AES key once and encrypt all files in parallel with unique IVs, giving near-linear speedup.
- **Packet format:** `MAGIC(4) | VERSION(1) | SALT(16) | IV(12) | CIPHERTEXT`, base64-encoded. Version = 0x02.
- **Zero network during crypto.** No server calls during encrypt or decrypt. Your words never leave your browser.

## Please read before using

- **There is no password recovery.** Lose the 6 words and the file is gone forever. Write them down.
- **Tell the recipient three things:** the 6 cipher words, the **ace mode** used at seal time, and where to fetch the ciphertext.
- **Send all three through different channels** if you can. If words + ciphertext + mode all land in the same chat, the encryption protects you from almost nothing.
- **Do not use this tool for any illegal purpose.** It exists for sharing personal AI-generated anime art among a small private group. Using it to transmit illegal content, harass anyone, distribute material that violates someone's rights, or break local laws is against the spirit of this project. Any such use is entirely the user's responsibility — not the author's.
- Max file size is around 80 MB per file (browser memory limit).

---

# 哈卡利共享 (中文)

一个浏览器端的小工具，用来在小范围的朋友圈里分享我用 AI 制作的二次元插画。

**在线地址：** https://slimpigs.github.io/hikaligroupshare/

仅供学习和玩耍。趣味加密，希望你爸不会发现。

---

## 这是什么

一个独立的单页 HTML（Sky Striker // Roze 中继器），在你的浏览器里用 **AES-256-GCM** 加密任何文件，密码是 **6 个单词**，输出一个 `.ssae.txt` 密文文本。你可以把这个文件丢到任何地方 —— GitHub raw、gist、paste 站、网盘直链都行 —— 然后通过另一个渠道把 6 个单词告诉对方。对方在同一个页面粘贴链接或拖入文件、输入相同的 6 个单词，就能还原原始文件。

整个加解密过程都在浏览器里完成，密码不会离开你的标签页。给你发密文的朋友看不到你的密码，托管密文的服务器也看不到你的原文件。

## 内容守则

**从现在起，本仓库只存放 SFW（适合工作场所观看）内容。** 工具本身是通用的加密工具 —— 任何人都可以在自己的电脑上用它来加密任何文件 —— 但是托管在**本仓库**里的 `.ssae.txt` 密文，从此只会是 AI 生成的**纯洁向**二次元插画。早期可能包含 NSFW 内容的密文已经从仓库及 git 历史中彻底清除。

无论如何，通过本工具分享的内容都仅限在本朋友圈内部欣赏，请不要向外传播。

## Ace 模式（新增）

页面顶部新增了 **ACE SELECT** 选择栏，可在六个 Sky Striker Ace 身份中切换：

| 模式 | 飞行员 | 装备 | 主色 |
|---|---|---|---|
| **Raye** | Raye | 基础形态 | 天蓝 |
| **Kagari** | Raye | Kagari 火甲 | 火橙 |
| **Shizuku** | Raye | Shizuku 水甲 | 电子青 |
| **Hayate** | Raye | Hayate 风甲 | 翡翠绿 |
| **Roze** | Roze | 基础形态 | 玫瑰品红 |
| **Zeke** | Roze | Zeke 重甲 | 深红＋金 |

**每个模式有自己的专属 pepper（加密配料）**。这意味着 **ace 模式本身是加密的第二道因素** —— 在 Kagari 模式下封印的 `.ssae.txt`，接收方**也必须切换到 Kagari 模式**才能解密。模式不对就直接报错，没有回退方案。

所以分享密文时，要告诉接收方三样东西：6 个密码词、**加密时使用的 ace 模式**、密文的获取地址。（双方都要在 ACE SELECT 栏点中同一个模式再开始封印/破封。）

## 使用方法

### 加密（发送方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. **在顶部 ACE SELECT 栏选好 ace 模式**。记住它 —— 等下要告诉接收方。
3. 停留在 **SEAL（封印）** 标签页（Loadout 01）。
4. 拖入图片 / 视频 / 任意文件，或点击面板选择。**可同时拖入多个文件**，会并行加密。每个文件最大约 80 MB。
5. 在密码格中填入你的 **6 个密码词**。这就是密钥 —— 请通过和密文**不同的渠道**告诉接收方。
6. 点击 **Engage Seal**。稍等片刻，加密结果会以列表形式出现，每个文件一行。
7. 每行的 **Save** 按钮保存单个 `.ssae.txt`，**Save All** 批量下载全部；**Copy** 复制单个密文，**Copy All as JSON** 把所有密文打包成一段 JSON 方便发到聊天里。然后把文件托管到任何地方，把直链发给对方。
8. **告诉接收方三样**：6 个密码词、你加密时所在的 ace 模式、密文的获取地址。

### 解密（接收方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. **把 ACE SELECT 切到发送方加密时使用的模式**。这一步必须做 —— 模式不对就解不开。
3. 切到 **BREACH（破封）** 标签页（Loadout 02）。
4. 三选一：
   - 把 `.ssae.txt` 的链接粘到 URL 框，点 **Fetch**；
   - 直接把密文粘进输入框；
   - 把下载好的 `.ssae.txt` 拖到面板上。
5. 填入发送方给你的 **6 个密码词**（顺序要一致）。
6. 点击 **Breach Transmission**。还原后的文件会在下方预览，点 **Download Recovered File** 保存到本地。

如果报错说 *"sealed under a different ace mode"*（封印于另一 ace 模式），把 ACE SELECT 切到发送方使用的模式再试一次。

## 技术细节

- **加密算法**：AES-256-GCM（带完整性校验，篡改会被发现）
- **密钥推导**：PBKDF2 / SHA-256 / 31 万次迭代
- **Salt 与 IV**：每次加密随机生成（16 / 12 字节）
- **模式专属 pepper**：每个 ace 模式有自己的 pepper（密码混入串），加入到密码词中后再进行 PBKDF2。六个模式 = 六个互不兼容的加密命名空间，模式本身成为第二认证因素。
- **并行封印**：批量上传时只推导一次 AES 密钥，所有文件用各自独立的 IV 并行加密，近似线性加速。
- **数据包格式**：`MAGIC(4) | VERSION(1) | SALT(16) | IV(12) | 密文`，整体 base64 编码。Version = 0x02。
- **加解密过程零网络**。没有任何服务器请求，密码词不会离开你的浏览器。

## 重要提示 — 请认真阅读

- **没有"忘记密码"功能**。如果 6 个密码词丢了，文件就再也打不开。请记好。
- **告诉接收方三样**：6 个密码词、封印时使用的 **ace 模式**、密文的获取地址。
- **三样东西尽量分开传**。如果密码词、密文、模式三样都在同一个聊天里发出去，加密相当于没起作用。
- **请勿用于任何非法用途**。本工具只是为了在一个小范围的私人朋友圈里分享 AI 生成的二次元插画。如果有人用它来传播违法内容、骚扰他人、侵犯他人权利、或者违反当地法律，责任完全在使用者本人，与作者无关。
- 单个文件大小上限约 80 MB（受浏览器内存限制）。
