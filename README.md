# 📋 Claude Code Clipboard Skill

> **Lightning-fast clipboard automation for developers across all platforms**

Transform your development workflow with instant command generation and cross-platform clipboard integration. No more hunting through documentation or remembering complex syntax—just ask and it's instantly in your clipboard, ready to paste.

## ✨ Features

🚀 **Zero-friction workflow** — Commands appear in clipboard instantly, no explanations or fluff  
🌍 **Universal platform support** — macOS, Linux (X11/Wayland), Windows, WSL, and Android (Termux)  
🎯 **Smart templates** — Pre-built shortcuts for common operations  
🛡️ **Safety first** — Automatic validation prevents dangerous commands  
⚡ **One-liner optimization** — Chains commands intelligently with `&&` and `;`  

## 🔧 Installation

1. **Clone or download** this skill to your Claude Code skills directory:
   ```bash
   git clone <this-repo> ~/.claude/skills/clipboard
   # OR manually place SKILL.md in ~/.claude/skills/clipboard/
   ```

2. **Restart Claude Code** to load the skill

3. **Start using** with `/clipboard` command!

## 🚀 Quick Start

```bash
# Get git commands instantly
/clipboard git reset last commit but keep changes

# Docker operations made simple  
/clipboard stop all docker containers

# File searches without the headache
/clipboard find all javascript files in src directory

# Use built-in templates
/clipboard git-add-commit-push
/clipboard docker-stop-all
```

## 📖 Usage Examples

### Git Operations
```bash
/clipboard git create new branch and switch to it
# → git checkout -b new-branch

/clipboard git-reset-keep
# → git reset HEAD~1

/clipboard undo last git commit
# → git reset HEAD~1
```

### File Operations
```bash
/clipboard find all python files excluding venv
# → find . -name '*.py' -not -path './venv/*' -not -path './.venv/*'

/clipboard compress all log files
# → tar -czf logs-$(date +%Y%m%d).tar.gz *.log
```

### Docker Management
```bash
/clipboard docker-stop-all
# → docker stop $(docker ps -q)

/clipboard remove all docker containers
# → docker rm $(docker ps -aq)

/clipboard docker cleanup images
# → docker image prune -a
```

### Development Workflows
```bash
/clipboard npm-clean
# → rm -rf node_modules package-lock.json && npm install

/clipboard kill process on port 3000
# → kill -9 $(lsof -ti:3000)

/clipboard git-branch-clean
# → git branch --merged | grep -v main | xargs git branch -d
```

## 🎯 Built-in Templates

| Template | Expands To | Description |
|----------|------------|-------------|
| `git-add-commit-push` | `git add . && git commit -m 'update' && git push` | Quick commit workflow |
| `git-reset-keep` | `git reset HEAD~1` | Undo last commit, keep changes |
| `docker-stop-all` | `docker stop $(docker ps -q)` | Stop all running containers |
| `docker-remove-all` | `docker rm $(docker ps -aq)` | Remove all containers |
| `find-js` | `find . -name '*.js' -not -path './node_modules/*'` | Find JS files (exclude node_modules) |
| `find-py` | `find . -name '*.py' -not -path './venv/*' -not -path './.venv/*'` | Find Python files (exclude venv) |
| `npm-clean` | `rm -rf node_modules package-lock.json && npm install` | Clean npm install |
| `git-branch-clean` | `git branch --merged \| grep -v main \| xargs git branch -d` | Clean merged branches |

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