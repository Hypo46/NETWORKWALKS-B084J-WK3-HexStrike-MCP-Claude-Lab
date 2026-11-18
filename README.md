# AI-Assisted Cybersecurity Lab: HexStrike AI MCP + Claude Desktop + John the Ripper

![Platform](https://img.shields.io/badge/Platform-Kali%20Linux-557C94?logo=kalilinux&logoColor=white)
![Protocol](https://img.shields.io/badge/Protocol-MCP-6E56CF)
![Python](https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white)
![Status](https://img.shields.io/badge/Status-Educational%20Lab-success)
![License](https://img.shields.io/badge/Use-Authorized%20Lab%20Only-orange)

A two-module practical lab demonstrating an **AI-assisted penetration testing workflow**: connecting an AI chat client (Claude Desktop) to offensive-security tooling through the **Model Context Protocol (MCP)**, using the **HexStrike AI MCP server** as the bridge, and then driving a real tool — **John the Ripper** — through that bridge to recover the password on an authorized lab PDF.

This repository is a personal lab writeup for the **NetworkWalks Cybersecurity Program (Batch B084J)**, documenting work performed on my own Kali Linux VM.

---

<details>
<summary><strong>📌 Documentation notes & assumptions (click to expand)</strong></summary>

- **Screenshot captions:** Each screenshot below is placed in the order its filename timestamp suggests, with a caption describing the *expected* step at that point in the workflow. The exact visual content of each of the 26 lab screenshots hasn't been individually reviewed yet — swap in the real caption for any image where it doesn't match what's actually shown.
- **MCP config:** The `claude_desktop_config.json` block in Module 01 is transcribed directly from the lab's own screenshot (not the generic example from HexStrike AI's public README) — it's the real config used in this session.
- **Root lab-report filenames:** `W3-PM1 - Week3 - Project Module1 - Passwor...` and `W3-PM2 - Week3 - Project Module2 - Passwor...` are shown truncated in the GitHub file list — complete the exact filenames below once you can see the full names.
- **`PDF/` folder contents:** Referenced below as holding supporting PDF assets (e.g. the target lab PDF) — confirm/adjust what's actually inside it.

</details>

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Objectives](#objectives)
3. [Learning Outcomes](#learning-outcomes)
4. [Lab Environment](#lab-environment)
5. [Tools and Technologies](#tools-and-technologies)
6. [Architecture](#architecture)
7. [Repository Structure](#repository-structure)
8. [Module 01 — HexStrike AI MCP Server Setup with Claude Desktop](#module-01--hexstrike-ai-mcp-server-setup-with-claude-desktop)
9. [Module 02 — AI-Assisted PDF Password Recovery with John the Ripper](#module-02--ai-assisted-pdf-password-recovery-with-john-the-ripper)
10. [Hashing vs. Password-Hash Extraction](#hashing-vs-password-hash-extraction)
11. [Results and Lab Completion](#results-and-lab-completion)
12. [Troubleshooting & Observations](#troubleshooting--observations)
13. [Security and Ethical Considerations](#security-and-ethical-considerations)
14. [References](#references)
15. [Credits](#credits)
16. [Author](#author)

---

## Project Overview

Most penetration-testing tool usage is still manual: the operator runs a tool, reads the output, and decides the next command. This lab explores the alternative — letting an AI chat client **drive** real security tools through a standardized protocol, while the human operator stays in control of what's authorized and what's executed.

The project is split into two connected modules:

| Module | Focus |
|---|---|
| **Module 01** | Stand up the AI ↔ tooling bridge: install Claude Desktop on Kali, deploy the HexStrike AI MCP server, and connect them via MCP configuration. |
| **Module 02** | Use that bridge for a real task: identify, hash, and recover the password of an authorized lab PDF using John the Ripper and the rockyou wordlist — driven entirely through natural-language prompts to Claude Desktop. |

## Objectives

- Stand up a working **MCP integration** between an AI chat client and a cybersecurity automation server.
- Understand how an AI agent selects and invokes command-line security tools through MCP rather than an operator typing them directly.
- Perform an authorized password-recovery exercise against a lab-provided PDF.
- Document the difference between a general file-integrity hash and a password/credential hash suitable for offline cracking.
- Produce a reproducible, evidence-backed writeup suitable for a cybersecurity learning portfolio.

## Learning Outcomes

- Practical experience configuring **Model Context Protocol (MCP)** servers and clients.
- Hands-on exposure to **HexStrike AI**, an MCP-based offensive-security automation framework.
- Reinforced understanding of **John the Ripper** workflows: hash extraction, wordlist attacks, and result verification.
- Awareness of the operational and ethical guardrails needed when AI agents are given access to security tooling.

## Lab Environment

| Component | Detail |
|---|---|
| Host OS | Windows 11 |
| Virtualization | VMware Workstation 17 Player |
| Guest OS (lab VM) | Kali Linux |
| AI Client | Claude Desktop |
| Integration | Model Context Protocol (MCP) |

## Tools and Technologies

| Category | Tool / Technology |
|---|---|
| Operating System | Kali Linux (VMware VM) |
| AI Client | Claude Desktop |
| Integration Layer | Model Context Protocol (MCP) |
| Security Automation Server | [HexStrike AI MCP](https://github.com/0x4m4/hexstrike-ai) |
| Password Recovery | John the Ripper (JTR) |
| Wordlist | `rockyou.txt` |
| Target Artifact | Authorized, password-protected lab PDF |
| Version Control | Git / GitHub |

## Architecture

```mermaid
flowchart TD
    subgraph VM["Kali Linux VM (VMware Workstation 17)"]
        CD["Claude Desktop<br/>(AI chat client)"]
        CFG["claude_desktop_config.json<br/>(MCP client config)"]
        MCPC["hexstrike_mcp.py<br/>(MCP client bridge)"]
        HX["HexStrike AI MCP Server<br/>hexstrike_server.py :8888"]
        TOOLS["150+ Security Tools<br/>(orchestrated by HexStrike)"]
        JTR["John the Ripper"]
        WL[("rockyou.txt<br/>wordlist")]
        PDFFILE[("Target PDF<br/>hash3.networkwalks_flag1.pdf")]
    end

    CD -->|reads config| CFG
    CFG -->|launches| MCPC
    MCPC <-->|MCP protocol| HX
    HX -->|invokes| TOOLS
    TOOLS --> JTR
    WL --> JTR
    PDFFILE --> JTR
    JTR -->|cracked password + flag| CD

    classDef mod1 fill:#1a73e8,color:#ffffff,stroke:#0b3d91;
    classDef mod2 fill:#d93025,color:#ffffff,stroke:#7a1b12;
    class CD,CFG,MCPC,HX mod1
    class TOOLS,JTR,WL,PDFFILE mod2
```

**Module 01** (blue) establishes the environment and the AI↔tool integration. **Module 02** (red) demonstrates practical tool interaction — the PDF recovery task — running through that already-configured bridge.

## Repository Structure

> Reflects the actual repo layout — screenshots live directly inside each module's folder (no separate `screenshots/` subfolder), filenames unchanged from capture.

```text
NETWORKWALKS-B084J-WK3-HexStrike-MCP-Claude-Lab/
│
├── README.md
│
├── HexStrike Claude/
│   ├── 7.2.1. LAB PRACTICE - How to setup Hexstrike MCP ....pdf
│   ├── Screenshot 2026-11-16 031657.png
│   ├── Screenshot 2026-11-16 031717.png
│   ├── Screenshot 2026-11-16 031740.png
│   ├── Screenshot 2026-11-16 032516.png
│   ├── Screenshot 2026-11-16 032539.png
│   ├── Screenshot 2026-11-16 032607.png
│   ├── Screenshot 2026-11-16 032719.png
│   ├── Screenshot 2026-11-16 032830.png
│   ├── Screenshot 2026-11-16 032907.png
│   ├── Screenshot 2026-11-16 033436.png
│   ├── Screenshot 2026-11-16 033450.png
│   ├── Screenshot 2026-11-16 033636.png
│   ├── Screenshot 2026-11-16 033925.png
│   ├── Screenshot 2026-11-16 034635.png
│   ├── Screenshot 2026-11-16 034653.png
│   ├── Screenshot 2026-11-16 034854.png
│   ├── Screenshot 2026-11-16 042142.png
│   ├── Screenshot 2026-11-16 044146.png
│   └── Screenshot 2026-11-16 044157.png
│
├── JTR Claude/
│   ├── Screenshot 2026-11-18 052242.png
│   ├── Screenshot 2026-11-18 052406.png
│   ├── Screenshot 2026-11-18 052700.png
│   ├── Screenshot 2026-11-18 053031.png
│   ├── Screenshot 2026-11-18 053104.png
│   ├── Screenshot 2026-11-18 053247.png
│   ├── Screenshot 2026-11-18 053436.png
│   ├── Screenshot 2026-11-18 053517.png
│   └── z. Optional Module Lab - JTR Password Cracking Lab v1 (AI-version).pdf
│
├── PDF/
│   └── (supporting PDF assets — e.g. target lab PDF; confirm exact contents)
│
├── W3-PM1 - Week3 - Project Module1 - Passwor....pdf   (complete exact filename)
└── W3-PM2 - Week3 - Project Module2 - Passwor....pdf   (complete exact filename)
```

---

## Module 01 — HexStrike AI MCP Server Setup with Claude Desktop

### Objective
Install Claude Desktop on Kali Linux, deploy the HexStrike AI MCP server, and connect the two so Claude Desktop can invoke HexStrike's tools through MCP.

### Environment
Kali Linux VM (VMware Workstation 17 Player), Python 3, internet access for package/repo downloads.

### Prerequisites
- A working Kali Linux installation with a desktop environment.
- `git`, `python3`, `python3-venv`, and `pip3` available.
- Outbound internet access for `apt`, `git clone`, and `pip install`.

### Tools Used
Claude Desktop (via the `aaddrick/claude-desktop-debian` community packaging project) and the HexStrike AI MCP server.

> Claude Desktop's official distribution is published by Anthropic; `aaddrick/claude-desktop-debian` is an independent, community-maintained packaging project that repackages it for Debian-based Linux distributions such as Kali. It is **not** an official Anthropic package.

### Installation and Configuration

**Step 1 — Install Claude Desktop (Debian/Kali via APT repo)**

```bash
curl -fsSL https://aaddrick.github.io/claude-desktop-debian/KEY.gpg | sudo gpg --dearmor -o /usr/share/keyrings/claude-desktop.gpg
echo "deb [signed-by=/usr/share/keyrings/claude-desktop.gpg arch=amd64,arm64] https://aaddrick.github.io/claude-desktop-debian stable main" | sudo tee /etc/apt/sources.list.d/claude-desktop.list
sudo apt update
sudo apt install claude-desktop
```

> ⚠️ Depending on when the lab was performed, this project ships the package as `claude-desktop` or `claude-desktop-unofficial`. Use whichever name your own terminal output/screenshots show.

**Step 2 — Clone and set up HexStrike AI**

```bash
git clone https://github.com/0x4m4/hexstrike-ai.git
cd hexstrike-ai

python3 -m venv hexstrike-env
source hexstrike-env/bin/activate

pip3 install -r requirements.txt
```

**Step 3 — Start the HexStrike AI MCP server**

```bash
python3 hexstrike_server.py
```

**Step 4 — Verify the server is running**

```bash
curl http://127.0.0.1:8888/health
```

**Step 5 — Configure Claude Desktop to use the HexStrike MCP server**

Edit `~/.config/Claude/claude_desktop_config.json` (this is the actual configuration used in this lab):

```json
{
  "mcpServers": {
    "hexstrike-ai": {
      "command": "/home/kali/hexstrike-ai/hexstrike-env/bin/python",
      "args": [
        "/home/kali/hexstrike-ai/hexstrike_mcp.py",
        "--server",
        "http://127.0.0.1:8888"
      ]
    }
  }
}
```

> This points `command` straight at the virtual environment's Python interpreter (rather than a bare `python3`), which avoids having to activate the venv before Claude Desktop launches it. Paths are specific to this machine — adjust `/home/kali/hexstrike-ai/...` to match your own clone location.

**Step 6 — Restart Claude Desktop** so it picks up the new MCP server, then open a chat and confirm HexStrike's tools are available to it.

### Screenshot Evidence — Module 01

*(Captions describe the expected step based on filename order — confirm against each image.)*

#### Lab start
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20031657.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20031657.png" alt="Lab start" width="700"></a>

#### HexStrike AI repository clone
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20031717.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20031717.png" alt="HexStrike repo clone" width="700"></a>

#### Python virtual environment creation
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20031740.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20031740.png" alt="Virtual environment creation" width="700"></a>

#### Dependency installation
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032516.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032516.png" alt="pip install dependencies" width="700"></a>

#### HexStrike server startup
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032539.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032539.png" alt="HexStrike server startup" width="700"></a>

#### Server health check
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032607.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032607.png" alt="Server health check" width="700"></a>

#### Claude Desktop installation
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032719.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032719.png" alt="Claude Desktop installation" width="700"></a>

#### Claude Desktop launched
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032830.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032830.png" alt="Claude Desktop launched" width="700"></a>

#### MCP config file being edited
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20032907.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20032907.png" alt="Editing claude_desktop_config.json" width="700"></a>

#### MCP config saved / Claude Desktop restarted
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20033436.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20033436.png" alt="MCP config saved" width="700"></a>

#### Claude Desktop recognizing the HexStrike MCP server
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20033450.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20033450.png" alt="MCP server recognized" width="700"></a>

#### Verification prompt/response in Claude Desktop
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20033636.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20033636.png" alt="Verification prompt" width="700"></a>

#### Tool list / capability confirmation
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20033925.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20033925.png" alt="Tool list confirmation" width="700"></a>

#### Additional configuration / verification
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20034635.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20034635.png" alt="Additional verification" width="700"></a>

#### Additional configuration / verification
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20034653.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20034653.png" alt="Additional verification" width="700"></a>

#### Additional configuration / verification
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20034854.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20034854.png" alt="Additional verification" width="700"></a>

#### Final environment check
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20042142.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20042142.png" alt="Final environment check" width="700"></a>

#### Module 01 wrap-up
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20044146.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20044146.png" alt="Module 01 wrap-up" width="700"></a>

#### Module 01 completion
<a href="HexStrike%20Claude/Screenshot%202026-11-16%20044157.png"><img src="HexStrike%20Claude/Screenshot%202026-11-16%20044157.png" alt="Module 01 completion" width="700"></a>

### Observations
Setting up the MCP bridge is primarily a one-time configuration task (path correctness in the JSON config is the most common failure point). Once connected, HexStrike's tool catalog becomes available to Claude Desktop as callable MCP tools rather than shell commands the operator types manually.

### Result
Claude Desktop successfully connected to the HexStrike AI MCP server, confirming the environment was ready for Module 02's practical task.

---

## Module 02 — AI-Assisted PDF Password Recovery with John the Ripper

### Objective
Use the MCP-connected environment from Module 01 to recover the password on an authorized, lab-provided PDF, driving John the Ripper entirely through natural-language prompts to Claude Desktop.

### Environment
Same Kali Linux VM, with the HexStrike AI MCP server running and Claude Desktop connected (per Module 01).

### Prerequisites
- Module 01 completed and the MCP connection confirmed working.
- John the Ripper installed (ships by default on Kali Linux).
- `rockyou.txt` wordlist available (commonly pre-installed, often compressed, under `/usr/share/wordlists/`).
- The target lab PDF placed on the Kali Desktop (per the lab manual).

### Workflow

**Step 1 — Lab file preparation**
The supplied lab PDF was placed on the Kali Linux Desktop at `/home/kali/Desktop/hash3.networkwalks_flag1.pdf`.

**Step 2 — Verify John the Ripper is available**
Rather than running `john --version` manually, the check was requested through Claude Desktop via the HexStrike MCP connection.

**Step 3 — Calculate the file hash**
A hash of the target PDF was requested — useful for file identification/integrity, and distinct from the password hash used for cracking (see [Hashing vs. Password-Hash Extraction](#hashing-vs-password-hash-extraction)).

**Step 4 — Password recovery workflow**
John the Ripper was invoked through the HexStrike MCP integration against the PDF's extracted password hash, using the `rockyou.txt` wordlist.

**Step 5 — Verify the recovered result**
The recovered password was used to confirm the PDF opened successfully, verifying the crack was correct.

**Step 6 — Lab flag**
The flag contained in the lab PDF was retrieved upon successful access (see [Results and Lab Completion](#results-and-lab-completion)).

### AI Prompts Used

> **Tool verification prompt:**
> Check if John the Ripper is installed in this HexStrike MCP and show me its version.

> **File hashing prompt:**
> Please calculate the hash value of this PDF file: `/home/kali/Desktop/hash3.networkwalks_flag1.pdf`

> **Password recovery prompt:**
> Please use the JTR tool in this HexStrike MCP server to crack the password of this PDF file. Use the rockyou.txt wordlist dictionary.

### Screenshot Evidence — Module 02

*(Captions describe the expected step based on filename order — confirm against each image.)*

#### Lab PDF placed on Desktop
<a href="JTR%20Claude/Screenshot%202026-11-18%20052242.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20052242.png" alt="Lab PDF on Desktop" width="700"></a>

#### JTR installation / version check
<a href="JTR%20Claude/Screenshot%202026-11-18%20052406.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20052406.png" alt="JTR version check" width="700"></a>

#### Hash calculation prompt and response
<a href="JTR%20Claude/Screenshot%202026-11-18%20052700.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20052700.png" alt="Hash calculation" width="700"></a>

#### Password recovery prompt issued
<a href="JTR%20Claude/Screenshot%202026-11-18%20053031.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20053031.png" alt="Password recovery prompt" width="700"></a>

#### JTR running against the PDF via HexStrike MCP
<a href="JTR%20Claude/Screenshot%202026-11-18%20053104.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20053104.png" alt="JTR running" width="700"></a>

#### JTR cracking progress with rockyou.txt
<a href="JTR%20Claude/Screenshot%202026-11-18%20053247.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20053247.png" alt="JTR cracking progress" width="700"></a>

#### Recovered password returned by Claude Desktop
<a href="JTR%20Claude/Screenshot%202026-11-18%20053436.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20053436.png" alt="Recovered password" width="700"></a>

#### PDF opened with recovered password / flag visible
<a href="JTR%20Claude/Screenshot%202026-11-18%20053517.png"><img src="JTR%20Claude/Screenshot%202026-11-18%20053517.png" alt="PDF opened, flag visible" width="700"></a>

### Result
The PDF's password was successfully recovered via JTR + rockyou.txt, driven entirely through AI prompts to Claude Desktop over the HexStrike MCP connection, and the recovery was confirmed by reopening the file.

---

## Hashing vs. Password-Hash Extraction

Two different kinds of "hash" come up in this lab, and they serve different purposes:

- **File-identification hash** (e.g., SHA-256/MD5 of the whole PDF file): a checksum used to verify a file hasn't changed or to uniquely identify a specific file. It says nothing about the file's password.
- **Password/credential hash**: a value derived from the PDF's own encryption metadata (its security handler), which represents the password in a form that can be tested offline. Tools like John the Ripper don't attack the PDF directly — they attack this extracted hash, trying candidate passwords (from a wordlist like `rockyou.txt`) until one produces a match.

This is why "hashing the file" (Step 3) and "cracking the password" (Step 4) are two separate actions in the workflow above, not the same step.

## Results and Lab Completion

**Module 01:** MCP bridge between Claude Desktop and the HexStrike AI MCP server established and verified.

**Module 02:** PDF password successfully recovered via John the Ripper (rockyou.txt wordlist) through the HexStrike MCP integration, confirmed by reopening the recovered PDF.

**Flags captured:**

```text
nw{networkwalks_flag1_jtr_270521_1}
nw{networkwalks_persistence_jtr_270521}
```

**Demo videos (LinkedIn):**
- [Part 1](https://www.linkedin.com/feed/update/urn:li:ugcPost:7528622429221318656/)
- [Part 2](https://www.linkedin.com/feed/update/urn:li:activity:7528624443938529280/)

## Troubleshooting & Observations

- The most common failure point in Module 01 is an incorrect absolute path in `claude_desktop_config.json` — the MCP server won't be picked up if the path to `hexstrike_mcp.py` (or the venv's Python interpreter) doesn't match the actual clone location.
- Claude Desktop must be fully restarted (not just the chat window closed) after editing the MCP config for changes to take effect.
- Large wordlists like `rockyou.txt` are sometimes shipped compressed (`rockyou.txt.gz`) on Kali and need to be extracted before use.

## Security and Ethical Considerations

This project was carried out in a controlled, educational lab environment as part of the NetworkWalks Cybersecurity Program. All tooling was used only against lab-provided or self-owned files — specifically the password-protected PDF supplied for this exercise.

- Tools demonstrated here (HexStrike AI, John the Ripper) must only be used against systems, files, or accounts for which you have **explicit authorization**.
- Password recovery should only be performed on authorized lab files, your own files, or files for which recovery has been explicitly permitted by the owner.
- AI-assisted execution of security tooling should always be reviewed and controlled by a human operator — the AI selects and invokes tools, but the operator is accountable for what gets run.
- This repository does not authorize, condone, or provide a basis for unauthorized access, credential theft, or attacks against third-party systems.

## References

- HexStrike AI: [https://github.com/0x4m4/hexstrike-ai](https://github.com/0x4m4/hexstrike-ai)
- Claude Desktop (Debian/Linux packaging): [https://github.com/aaddrick/claude-desktop-debian](https://github.com/aaddrick/claude-desktop-debian)

## Credits

Lab exercises and training material for this project are based on coursework from **NetworkWalks Academy** (Batch B084J). The implementation, configuration, documentation, and screenshots in this repository are the author's own practical work completed as part of that training — the original training content itself was not authored by the repository owner.

## Author

**Rajatava Ghosh**
Cybersecurity student — NetworkWalks Cybersecurity Program (Batch B084J)

- GitHub: [RaxingR](https://github.com/RaxingR) (this lab repository is hosted under [Hypo46](https://github.com/Hypo46))
- LinkedIn: [rajatava-ghosh](https://www.linkedin.com/in/rajatava-ghosh-265aa6316/)
