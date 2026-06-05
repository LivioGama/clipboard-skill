# 📋 Clipboard Skills Suite

> **Lightning-fast clipboard automation for developers across all platforms**

Transform your development workflow with instant command generation and cross-platform clipboard integration. No more hunting through documentation or remembering complex syntax—just ask and it's instantly in your clipboard, ready to paste.

## 📦 Skills in This Suite

### 1. **clipboard** — Copy TO clipboard
Generate commands, code, or text and copy them directly to your clipboard. Perfect for sharing commands with colleagues or bypassing AI assistant formatting issues.

### 2. **from-clipboard** — Read FROM clipboard  
Read and process content from your clipboard. Analyze code, fix bugs, transform data, or explain text that's already copied.

## ✨ Features

🚀 **Natural language processing** — Understands conversational requests and generates appropriate commands  
🌍 **Universal platform support** — macOS, Linux (X11/Wayland), Windows, WSL, and Android (Termux)  
🎯 **Colleague sharing focus** — Perfect for unusual commands AND bypassing Claude Code formatting issues  
🔄 **Bidirectional clipboard** — Both copy TO and read FROM clipboard  
🛡️ **Safety first** — Automatic validation prevents dangerous commands  
⚡ **Context-aware optimization** — Chains commands intelligently with `&&` and `;`  

## 🔧 Installation

### Claude Code
**Modern Installation (Recommended):**
```bash
/skill-installer install from https://github.com/LivioGama/clipboard-skill
```

**Manual Installation:**

This repo ships **two** skills (`clipboard` and `from-clipboard`). Skills are only discovered as *top-level* directories under `~/.claude/skills/`, so install both:
```bash
git clone https://github.com/LivioGama/clipboard-skill ~/.claude/skills/clipboard
ln -s clipboard/from-clipboard ~/.claude/skills/from-clipboard
```

### Codex CLI
**Installation:**
```bash
# Clone to Codex skills directory, then surface from-clipboard as its own top-level skill
git clone https://github.com/LivioGama/clipboard-skill ~/.codex/skills/clipboard
ln -s clipboard/from-clipboard ~/.codex/skills/from-clipboard

# Or if using a different skills path, check your Codex configuration
git clone https://github.com/LivioGama/clipboard-skill ~/.config/codex/skills/clipboard
ln -s clipboard/from-clipboard ~/.config/codex/skills/from-clipboard
```

### Devin CLI
**Installation:**
```bash
# Clone to Devin skills directory, then surface from-clipboard as its own top-level skill
git clone https://github.com/LivioGama/clipboard-skill ~/.devin/skills/clipboard
ln -s clipboard/from-clipboard ~/.devin/skills/from-clipboard

# Or use the Devin skill installer if available
devin skill install https://github.com/LivioGama/clipboard-skill
```

### Other AI Coding Assistants
**Cursor, Continue, or similar:**
```bash
git clone https://github.com/LivioGama/clipboard-skill ~/.cursor/skills/clipboard
ln -s clipboard/from-clipboard ~/.cursor/skills/from-clipboard
# or wherever your AI assistant looks for skills
```

**Check your AI assistant's documentation for the correct skills directory path.**

After installation, restart your environment and start using with `/clipboard` and `/from-clipboard`!

## 🚀 Quick Start

### clipboard (Copy TO clipboard)
```bash
# Configure OpenCode to only use Copilot
/clipboard one-liner to configure OpenCode for Copilot only

# Kill and restart SSH agent
/clipboard restart stuck SSH agent

# Find large files for cleanup
/clipboard find all files larger than 100MB

# Kill process holding a file handle
/clipboard kill process holding file handle on specific file
```

### from-clipboard (Read FROM clipboard)
```bash
# Analyze code you've copied
/from-clipboard analyze this code for performance issues

# Fix bugs in copied code
/from-clipboard fix the bugs in this code

# Convert code to another language
/from-clipboard convert this JavaScript to TypeScript

# Explain what copied code does
/from-clipboard explain what this function does
```

## 📖 Usage Examples

### Colleague Sharing Commands
```bash
/clipboard one-liner to configure OpenCode for Copilot only
# → mkdir -p ~/.config/opencode && echo '{"$schema":"https://opencode.ai/config.json","enabled_providers":["github-copilot"],"provider_lock":true}' > ~/.config/opencode/opencode.json

/clipboard restart stuck SSH agent
# → pkill ssh-agent; eval $(ssh-agent -s); ssh-add ~/.ssh/id_rsa

/clipboard kill process holding file handle
# → lsof "$1" | awk 'NR>1 {print $2}' | xargs kill -9
```

### System Administration
```bash
/clipboard find files larger than 100MB
# → find . -size +100M -type f -exec ls -lh {} \;

/clipboard compress directory excluding dependencies
# → tar --exclude='node_modules' --exclude='vendor' -czf backup-$(date +%Y%m%d).tar.gz .

/clipboard check which process is using port
# → lsof -i :3000
```

### Network & Service Management
```bash
/clipboard restart Docker daemon when stuck
# → sudo systemctl restart docker || sudo service docker restart

/clipboard check SSL certificate expiration
# → echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates

/clipboard monitor real-time network connections
# → netstat -tulnp | grep :80
```

### Development Troubleshooting  
```bash
/clipboard reset file permissions recursively
# → find . -type f -exec chmod 644 {} \; && find . -type d -exec chmod 755 {} \;

/clipboard clear all DNS cache
# → sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder

/clipboard force kill all node processes
# → pkill -f node || killall node
```

### from-clipboard Processing
```bash
# Copy some code, then:
/from-clipboard analyze this code
# → Reads clipboard, provides analysis and suggestions

/from-clipboard fix the bugs
# → Reads clipboard, identifies and fixes bugs

/from-clipboard convert to TypeScript
# → Reads JS code, converts to TypeScript

/from-clipboard add error handling
# → Reads clipboard code, adds try-catch blocks

/from-clipboard format this JSON
# → Reads clipboard JSON, pretty-prints it
```

## 🎯 Clean Commands for Sharing & Direct Use

The `/clipboard` skill specializes in generating clean, properly formatted commands perfect for:

**Colleague Sharing:**
- **Complex one-liners** that are hard to construct from scratch
- **System administration commands** that vary by platform  
- **Debugging commands** that you can never remember the exact syntax for
- **Configuration setups** that require specific directory structures and JSON formatting

**Claude Code Limitations:**
- **Long commands that break** when Claude Code adds unwanted line breaks and spaces
- **Commands Claude Code can't execute** due to system limitations or security constraints
- **Clean formatting** without markdown formatting or extra whitespace that breaks execution
- **Properly escaped strings** that work correctly when pasted into terminal

## 🌍 Platform Support

### Automatic Detection
The skill automatically detects your platform and uses the appropriate clipboard tool:

| Platform | Primary Tool | Fallbacks |
|----------|--------------|-----------|
| **macOS** | `pbcopy` | Manual copy instructions |
| **Linux (X11)** | `xclip` | `xsel` → manual instructions |
| **Linux (Wayland)** | `wl-copy` | `xclip` → `xsel` → manual |
| **Windows** | `clip.exe` | PowerShell → manual |
| **WSL** | `clip.exe` | `clip` → manual |
| **Android (Termux)** | `termux-clipboard-set` | Manual instructions |

### Installation Commands by Platform

**macOS:**
```bash
# pbcopy/pbpaste are built-in
```

**Linux (Ubuntu/Debian):**
```bash
# For X11
sudo apt install xclip

# For Wayland  
sudo apt install wl-clipboard
```

**Windows:**
```bash
# clip.exe is built-in
```

**Termux (Android):**
```bash
pkg install termux-api
```

## 🛡️ Safety Features

### Dangerous Command Detection
The skill automatically warns about potentially dangerous operations:

```bash
/clipboard delete everything in root
# ⚠️  WARNING: This command could be dangerous:
# rm -rf /
# 
# Are you sure? This will delete all files on your system.
# Use with caution: Consider using rm -rf ./specific-directory instead
```

**Patterns that trigger warnings:**
- `rm -rf /` or system-wide deletions
- `sudo rm -rf` commands  
- `chmod 777` on system directories
- Privileged operations without context

## 🎨 Design Philosophy

### Pure Output
```bash
# ❌ What other tools do
/clipboard git status
# → "Here's the git status command: git status"

# ✅ What this skill does  
/clipboard git status
# → git status (directly in clipboard)
```

### Smart Chaining
```bash
/clipboard add commit and push changes
# → git add . && git commit -m 'update' && git push

/clipboard install dependencies and start dev server
# → npm install && npm run dev
```

### Context Awareness
```bash
/clipboard docker logs for container webapp
# → docker logs webapp

/clipboard kill process using port 3000
# → kill -9 $(lsof -ti:3000)
```

## 🔧 Advanced Usage

### Custom Scripts
```bash
/clipboard bash script to backup database
# → Generates complete backup script with error handling

/clipboard python script to read csv and convert to json
# → Creates minimal Python script ready to run
```

### Complex Operations
```bash
/clipboard git rebase interactive last 3 commits
# → git rebase -i HEAD~3

/clipboard docker compose with postgres and redis
# → Generates docker-compose.yml content
```

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Add templates** — Submit commonly used command patterns
2. **Improve platform support** — Add support for new environments  
3. **Enhance safety** — Suggest dangerous command patterns to detect
4. **Fix bugs** — Report issues or submit fixes

### Development Setup
```bash
git clone <this-repo>
cd clipboard-skill

# Test the skill locally
cp -r . ~/.claude/skills/clipboard/
# Restart Claude Code and test with /clipboard
```

## 📝 License

MIT License — Use freely, modify as needed, contribute back if you can!

## 🙋 Support

- **Issues**: Report bugs or request features via GitHub Issues
- **Discussions**: Ask questions in GitHub Discussions  
- **Documentation**: Check the [Claude Code Skills Documentation](https://claude.com/docs/skills)

---

<div align="center">

**Made with ❤️ for developers who value efficiency**

[⭐ Star this repo](../../) if it saves you time!

</div>