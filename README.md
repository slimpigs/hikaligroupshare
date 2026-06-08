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

## MIL-SPEC mode (NEW)

Next to the language toggle there's a **MIL-SPEC** switch. It's a seventh, hardened mode — not an anime ace, and it deliberately looks nothing like them. Engage it and the whole console becomes a **phosphor-green hacker terminal**: the ace rail and the MUSE/RZ-7 core bay are stripped away, **Matrix-style digital rain** falls behind the panels, a `root@blacksite:~/skystriker` shell header appears, a fast typed **boot sequence** runs (`> ACCESS GRANTED — MIL-SPEC ONLINE`), and everything recolors to green-on-black with CRT flicker, a glitching/RGB-split title, blinking cursors, and a **scramble-reveal** animation when you roll a random key.

The cipher lock is rebuilt as a **12-segment KEY MATRIX** — a 4×3 keypad with hex segment addresses (`0x00`–`0x0B`) and a per-segment **ARMED** indicator that ignites as you enter each word. Below it, a live **KEY STRENGTH** meter fills and escalates its clearance grade as the matrix fills:

`INSUFFICIENT → GUARDED → HARDENED → BLACKSITE`

What it actually changes under the hood:

- **12-word cipher instead of 6.** A full MIL-SPEC key carries **≈ 155 bits** of entropy (vs. ≈ 78 for a 6-word ace key) — the EFF large diceware list at ~12.9 bits/word, twelve segments deep.
- **PBKDF2 is hardened from 310,000 → 1,000,000 iterations** — far slower to brute-force a weak passphrase.
- **Its own pepper**, exactly like the ace modes — a MIL-SPEC packet can ONLY be breached in MIL-SPEC mode.
- The iteration count is **written into the packet (v3 format)**, so the recipient just selects MIL-SPEC and breaches — no extra coordination needed beyond the mode + the **12 words**.

The word count *is* the mode: selecting MIL-SPEC renders 12 segments on both the seal and breach sides, so the recipient simply switches to MIL-SPEC and the grid asks for the right number of words. Pair it with **⚄ Generate** for a full ≈ 155-bit random key and it's genuinely strong.

> **Backward compatibility:** the six anime ace modes are unchanged — everything sealed before this update (v2 310K, or ace v3) still breaches normally. The one exception is anything sealed under the *original* 6-word MIL-SPEC: MIL-SPEC now expects 12 words, so re-seal those few packets if any exist.

## Random key generation + key card (NEW)

Under the cipher grid on the **SEAL** side:

- **⚄ Generate** — rolls a cryptographically-random key from the EFF large diceware list (7,776 words → **≈ 12.9 bits/word**), using `crypto.getRandomValues` with rejection sampling (no modulo bias). It fills however many segments the active mode uses — **6 words (≈ 78 bits)** in an ace mode, **12 words (≈ 155 bits)** in MIL-SPEC. The entropy estimate shows next to the button.
- **⎘ Copy** — copies the active key (6 or 12 words) to the clipboard.
- **⭳ Key Card** — downloads a small `skystriker-keycard-<timestamp>.txt` containing the words, the word count, the mode (ace or MIL-SPEC), the entropy estimate, and the KDF strength — everything the recipient needs. **Keep it offline; don't store it next to the sealed file.**

## How to use

### Sealing files (sender)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. **Pick your ace mode** in the ACE SELECT rail at the top. Remember it — you'll tell the recipient.
3. Stay on the **SEAL** tab (Loadout 01).
4. Drop one or more files onto the panel, or click to pick. Multiple files seal in parallel. File size is bounded only by your browser's memory budget (hundreds of MB per file work on desktop).
5. Fill in your cipher words in the lock grid — **6 words** in an ace mode, or **12 segments** in MIL-SPEC. This is the passphrase — share it with the recipient through a **different channel** from the ciphertext.
6. Click **Engage Seal**. The MUSE/RZ-7 chip above the tabs pulses red while AES-GCM is running; after a moment the sealed transmissions appear, one row per file.
7. Per-row buttons:
   - **Copy** — copy this cipher to clipboard
   - **Save** — download as `<original>.ssae.txt`
   - **Replace** *(Chrome / Edge / Opera / Brave only)* — overwrite the original file in place with the sealed version

   Batch buttons (bottom):
   - **Save All** — downloads every `.ssae.txt` sequentially
   - **Copy All as JSON** — bundles every cipher into a single chat-friendly JSON blob
   - **Save All as JSON** — downloads that bundle as one `.json` file (use this when the bundle is too big for the clipboard)

   Then host the file(s) wherever and send the link(s).
8. **Tell the recipient three things:** the cipher words (6, or **12 in MIL-SPEC**), the mode you sealed in, and where to fetch the ciphertext (a single `.ssae.txt`, a folder of them, or the bundle `.json`).

### Breaching files (recipient)

1. Open the [relay page](https://slimpigs.github.io/hikaligroupshare/).
2. **Set the ACE SELECT to the same mode the sender used.** This is required — wrong mode will fail to decrypt.
3. Switch to the **BREACH** tab (Loadout 02).
4. Get the ciphertext into the page — pick whichever fits:
   - **Single cipher**: paste a `.ssae.txt` URL into the URL field and click **Fetch**, paste the ciphertext directly into the input box, or drop the downloaded `.ssae.txt` onto the panel.
   - **Bundle (multi-cipher)**: paste the JSON bundle from "Copy All as JSON", or drop a `.json` bundle file onto the panel. The page detects the bundle and switches into multi-cipher mode automatically — you'll see every cipher listed as a row.
5. Type the same cipher words the sender gave you — the grid shows **6 slots** in an ace mode and **12 segments** in MIL-SPEC. Order matters.
6. Click **Breach Transmission** (single) or **Breach All** (bundle).
   - **Single**: recovered file previews in the panel; click **Download Recovered File** to save.
   - **Bundle**: each row decrypts in parallel and gets **Save** + **Preview** buttons. Footer has **Save All Recovered** and **Copy All Filenames**.

If breach fails with *"sealed under a different ace mode"*, switch the ACE SELECT to the mode the sender used and try again.

## Other UI notes

- **Language toggle**: top-right of the ACE SELECT rail. Click **中文 / EN** to swap the UI language. The choice persists across visits.
- **MUSE/RZ-7 chip** between the header and tabs is the visual processor — it pulses red and runs a scan-line sweep while seal / breach is actually working. The bus rails above and below it flow rightward during SEAL, leftward during BREACH.

## Technical details

- **Cipher:** AES-256-GCM (authenticated encryption — tampering is detected)
- **Key derivation:** PBKDF2 / SHA-256 — **310,000** iterations (standard ace modes) or **1,000,000** (MIL-SPEC)
- **Random key generation:** EFF large diceware wordlist (7,776 words), picked with `crypto.getRandomValues` + rejection sampling → ≈ 12.9 bits/word — **≈ 78 bits for a 6-word ace key, ≈ 155 bits for a 12-word MIL-SPEC key**
- **Word count is mode-bound:** ace modes use a 6-slot grid; MIL-SPEC renders a 12-segment key matrix on both seal and breach sides, with a live entropy/clearance meter (INSUFFICIENT → GUARDED → HARDENED → BLACKSITE)
- **Salt / IV:** randomly generated per encryption (16 / 12 bytes)
- **Per-mode pepper:** each mode (six aces + MIL-SPEC) has its own secret pepper baked into the page and appended to the password before PBKDF2. The mode therefore acts as a second factor.
- **Parallel sealing:** multi-file batches derive the AES key once and encrypt all files in parallel with unique IVs, giving near-linear speedup.
- **Parallel breaching:** when a JSON bundle is loaded, the receiver groups ciphers by salt (one PBKDF2 per unique salt — usually just one for a same-batch bundle), then runs all AES-GCM decrypts in parallel.
- **Packet format:** base64-encoded. **v3** (current): `MAGIC(4) | 0x03 | ITERATIONS(4, big-endian) | SALT(16) | IV(12) | CIPHERTEXT` — the KDF iteration count travels in the packet so any strength breaches without extra coordination. **v2** (`MAGIC | 0x02 | SALT | IV | CIPHERTEXT`, fixed 310K) is still fully readable, so older sealed files keep working.
- **Bundle format:** `[{"filename": "...", "cipher": "..."}, ...]` — a plain JSON array of single-cipher entries. Saved as `skystriker-bundle-<ISO-timestamp>-<N>files.json`.
- **Zero network during crypto.** No server calls during encrypt or decrypt. Your words never leave your browser.

## Please read before using

- **There is no password recovery.** Lose the words and the file is gone forever. Write them down.
- **Tell the recipient three things:** the cipher words (6, or **12 in MIL-SPEC**), the **mode** used at seal time, and where to fetch the ciphertext.
- **Send all three through different channels** if you can. If words + ciphertext + mode all land in the same chat, the encryption protects you from almost nothing.
- **Do not use this tool for any illegal purpose.** It exists for sharing personal AI-generated anime art among a small private group. Using it to transmit illegal content, harass anyone, distribute material that violates someone's rights, or break local laws is against the spirit of this project. Any such use is entirely the user's responsibility — not the author's.
- File size is bounded only by your browser's memory budget. Desktop Chrome/Firefox routinely handle several hundred MB per file; mobile browsers cap lower (~150 MB on iOS Safari is typical).

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

## 军规模式 MIL-SPEC（新增）

语言切换按钮旁边有一个 **MIL-SPEC** 开关。它是第七种、强化的模式 —— 不是动漫 ace，外观也刻意与它们完全不同。开启后整个控制台会变成一台**磷光绿黑客终端**：ace 栏和 MUSE/RZ-7 核心被隐藏，面板后方落下 **Matrix 风格数字雨**，顶部出现 `root@blacksite:~/skystriker` 命令行标题栏，并播放一段快速的**启动序列**（`> ACCESS GRANTED — MIL-SPEC ONLINE`），整体重新着色为绿黑配色，带 CRT 闪烁、标题故障（RGB 撕裂）效果、闪烁光标，以及随机生成密钥时的**字符乱码解码**动画。

密码锁会重建为一个 **12 段密钥矩阵** —— 4×3 键盘布局，每段带十六进制地址（`0x00`–`0x0B`）和一个 **ARMED（已装填）** 指示灯，输入每个词时点亮。下方有一条实时**密钥强度**条，随矩阵填满而升级密级：

`强度不足 → 基本 → 加固 → 最高机密`

底层实际变化：

- **12 个词，而非 6 个。** 一把完整的军规模式密钥承载 **约 155 位**熵（6 词 ace 密钥约 78 位）—— EFF 大词表每词约 12.9 位，十二段累计。
- **PBKDF2 从 310,000 轮提升到 1,000,000 轮** —— 弱口令被暴力破解会慢得多。
- **拥有独立 pepper**，和各 ace 模式一样 —— 军规模式封的包只能在军规模式下破封。
- 迭代次数**写入数据包（v3 格式）**，接收方只需选中 MIL-SPEC 即可破封，除模式 + **12 词**外无需额外协调。

词数即模式：选中 MIL-SPEC 后，封印侧和破封侧都会渲染 12 段，接收方只要切到 MIL-SPEC，密码格自然就会要求正确的词数。配合 **⚄ 随机生成** 得到完整的约 155 位随机密钥，强度十分扎实。

> **向后兼容：** 六个动漫 ace 模式保持不变 —— 本次更新之前封印的所有文件（v2 31 万，或 ace v3）仍可正常破封。唯一例外是用**最初的 6 词版**军规模式封印的文件：MIL-SPEC 现在要求 12 个词，若有这类旧包请重新封印。

## 随机密钥生成 + 密钥卡（新增）

在 **SEAL（封印）** 侧密码格下方：

- **⚄ 随机生成** —— 用 `crypto.getRandomValues`（拒绝采样、无取模偏差）从 EFF 大词表（7,776 词 → 每词约 **12.9 位**）生成随机密钥。会按当前模式填满对应段数 —— ace 模式 **6 个词（约 78 位）**，MIL-SPEC **12 个词（约 155 位）**，按钮旁显示熵估计。
- **⎘ 复制** —— 复制当前密钥（6 或 12 个词）到剪贴板。
- **⭳ 密钥卡** —— 下载 `skystriker-keycard-<时间戳>.txt`，内含密码词、词数、模式（ace 或 MIL-SPEC）、熵估计与 KDF 强度，破封所需信息齐全。**请离线保管，不要和密文放在一起。**

## 使用方法

### 封印（发送方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. **在顶部 ACE SELECT 栏选好 ace 模式**。记住它 —— 等下要告诉接收方。
3. 停留在 **SEAL（封印）** 标签页（Loadout 01）。
4. 拖入一个或多个文件，或点击面板选择。多文件并行加密。单文件大小仅受浏览器内存限制（桌面端通常能处理几百 MB）。
5. 在密码格中填入你的密码词 —— ace 模式 **6 个词**，MIL-SPEC **12 段**。这就是密钥 —— 请通过和密文**不同的渠道**告诉接收方。
6. 点击 **Engage Seal**。标签页上方的 MUSE/RZ-7 芯片会在 AES-GCM 运行时变红脉动；稍等片刻加密结果出现，每个文件一行。
7. 每行三个按钮：
   - **Copy** —— 复制密文到剪贴板
   - **Save** —— 下载为 `<原文件名>.ssae.txt`
   - **Replace** *(仅限 Chrome / Edge / Opera / Brave)* —— 就地覆盖原始文件为封印版

   下方批量按钮：
   - **Save All** —— 依次下载所有 `.ssae.txt`
   - **Copy All as JSON** —— 把所有密文打包成一段 JSON 方便发到聊天里
   - **Save All as JSON** —— 把这个包下载为一个 `.json` 文件（密文包太大不能粘贴时用这个）

   然后把文件托管到任何地方，把链接发给对方。
8. **告诉接收方三样**：密码词（6 个，**MIL-SPEC 为 12 个**）、你加密时所在的模式、密文的获取地址（单个 `.ssae.txt`、一组 `.ssae.txt`，或密文包 `.json`）。

### 破封（接收方）

1. 打开[中继页面](https://slimpigs.github.io/hikaligroupshare/)。
2. **把 ACE SELECT 切到发送方加密时使用的模式**。这一步必须做 —— 模式不对就解不开。
3. 切到 **BREACH（破封）** 标签页（Loadout 02）。
4. 把密文导入页面，选哪个都行：
   - **单个密文**：粘贴 `.ssae.txt` URL 到 URL 框点 **Fetch**、直接把密文粘进输入框、或把下载好的 `.ssae.txt` 拖到面板上。
   - **密文包（多文件）**：粘贴来自 "Copy All as JSON" 的 JSON 数组、或把 `.json` 包文件拖到面板上。页面自动识别并切换到批量模式 —— 每个密文显示为一行。
5. 填入发送方给你的密码词 —— 密码格在 ace 模式显示 **6 格**，MIL-SPEC 显示 **12 段**（顺序要一致）。
6. 点击 **Breach Transmission**（单个）或 **Breach All**（密文包）。
   - **单个**：还原文件在下方预览，点 **Download Recovered File** 保存。
   - **密文包**：每行并行解密，完成后出现 **Save** + **Preview** 按钮；底部有 **Save All Recovered** 和 **Copy All Filenames**。

如果报错说 *"sealed under a different ace mode"*（封印于另一 ace 模式），把 ACE SELECT 切到发送方使用的模式再试一次。

## 其他界面说明

- **语言切换**：ACE SELECT 栏右上角，点 **中文 / EN** 切换界面语言。选择会保存到本地。
- **MUSE/RZ-7 芯片**位于标题和标签页之间，是视觉处理器 —— 实际封印 / 破封运行时会变红脉动并出现扫描线效果。它上下的总线在 SEAL 模式下数据右流，BREACH 模式下左流。

## 技术细节

- **加密算法**：AES-256-GCM（带完整性校验，篡改会被发现）
- **密钥推导**：PBKDF2 / SHA-256 —— **31 万**次迭代（标准 ace 模式）或 **100 万**次（MIL-SPEC）
- **随机密钥生成**：EFF 大词表（7,776 词），`crypto.getRandomValues` + 拒绝采样 → 每词约 12.9 位 —— **6 词 ace 密钥约 78 位，12 词 MIL-SPEC 密钥约 155 位**
- **词数与模式绑定**：ace 模式用 6 格密码格；MIL-SPEC 在封印侧与破封侧都渲染 12 段密钥矩阵，并带一条实时熵 / 密级强度条（强度不足 → 基本 → 加固 → 最高机密）
- **Salt 与 IV**：每次加密随机生成（16 / 12 字节）
- **模式专属 pepper**：每个模式（六个 ace + MIL-SPEC）有自己的 pepper（密码混入串），加入到密码词中后再进行 PBKDF2，模式本身成为第二认证因素。
- **并行封印**：批量上传时只推导一次 AES 密钥，所有文件用各自独立的 IV 并行加密，近似线性加速。
- **并行破封**：加载 JSON 密文包时，接收端按 salt 分组（一组只跑一次 PBKDF2 —— 同批次封印的密文包通常只有一组），然后所有 AES-GCM 解密并行执行。
- **数据包格式**：base64 编码。**v3**（当前）：`MAGIC(4) | 0x03 | 迭代次数(4，大端) | SALT(16) | IV(12) | 密文` —— 迭代次数随包传递，任意强度都能直接破封。**v2**（`MAGIC | 0x02 | SALT | IV | 密文`，固定 31 万）仍可完整读取，旧密文继续可用。
- **密文包格式**：`[{"filename": "...", "cipher": "..."}, ...]` —— 单个密文条目的 JSON 数组。保存文件名为 `skystriker-bundle-<ISO 时间戳>-<N>files.json`。
- **加解密过程零网络**。没有任何服务器请求，密码词不会离开你的浏览器。

## 重要提示 — 请认真阅读

- **没有"忘记密码"功能**。如果密码词丢了，文件就再也打不开。请记好。
- **告诉接收方三样**：密码词（6 个，**MIL-SPEC 为 12 个**）、封印时使用的**模式**、密文的获取地址。
- **三样东西尽量分开传**。如果密码词、密文、模式三样都在同一个聊天里发出去，加密相当于没起作用。
- **请勿用于任何非法用途**。本工具只是为了在一个小范围的私人朋友圈里分享 AI 生成的二次元插画。如果有人用它来传播违法内容、骚扰他人、侵犯他人权利、或者违反当地法律，责任完全在使用者本人，与作者无关。
- 单个文件大小仅受浏览器内存限制。桌面端 Chrome/Firefox 通常可处理几百 MB；移动浏览器上限较低（iOS Safari 一般 ≤ ~150 MB）。
