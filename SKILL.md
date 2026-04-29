---
name: clipboard
description: >
  This skill should be used when the user asks to "copy to clipboard", "put in clipboard", 
  mentions "/clipboard", wants command line output copied, or needs text/code placed in system clipboard.
  Also triggered by requests for quick command generation that should be immediately usable.
version: 2.0.0
---

# Cross-Platform Clipboard Skill

**Slash command:** `/clipboard`

**Description:** Clean, optimized command line or text output directly to clipboard without explanations. Works across all platforms including headless servers via OSC 52, tmux buffers, and fallback file sharing.

## Platform Support

- **macOS**: `pbcopy` / `pbpaste`
- **Linux**: `xclip` / `xsel` / `wl-copy` (Wayland)
- **Windows**: `clip.exe` / PowerShell
- **WSL**: `clip.exe`
- **Termux**: `termux-clipboard-set`
- **Headless Servers**: OSC 52 escape sequences, tmux buffers, temp file sharing
- **SSH/Remote**: Forward clipboard via OSC 52 to local terminal

## Usage

```bash
/clipboard one-liner to configure Copilot in OpenCode
/clipboard command to restart stuck SSH agent
/clipboard find all files larger than 100MB
/clipboard kill process holding a file handle
/clipboard compress directory excluding node_modules
/clipboard one-liner to prevent colleague from using other AI providers in OpenCode
```

## Behavior

- **Natural language processing** — Understands conversational requests and generates appropriate commands
- **One-liner focus** — Optimized for sharing unusual, hard-to-find commands with colleagues AND bypassing Claude Code formatting issues
- **Copy to clipboard then display** — Execute clipboard command then show what was copied
- **NO "here's the command" or similar preamble before copying**
- **Show copied content after clipboard operation** — Confirm what was successfully copied
- **Optimized for command sharing** — Perfect for Slack, Discord, or any communication platform AND when Claude Code breaks long commands with unwanted formatting
- **Auto-detects platform and uses appropriate clipboard tool**
- **Single line preferred when possible**
- **Chain commands intelligently** (use `&&` or `;` when beneficial)
- **Multiple lines only when necessary for readability**
- **Security validation for dangerous commands** (warns with confirmation)
- **Context-aware command optimization**

## Headless Server Support

The skill includes advanced support for headless environments and remote servers:

### OSC 52 Escape Sequences
- **What**: Terminal escape sequence protocol for clipboard access
- **Works with**: iTerm2, Alacritty, Wezterm, Windows Terminal, and many modern terminals  
- **Benefit**: Copy to clipboard over SSH without forwarding or GUI
- **Usage**: Automatically detected when no GUI clipboard tools available

### Tmux Integration
- **Tmux buffers**: Automatically uses tmux clipboard when available
- **Session sharing**: Other tmux panes can access the copied content
- **Retrieve**: Use `tmux show-buffer` to view copied content

### Fallback File Sharing
For completely isolated environments, the skill provides multiple manual copy options:
- **SCP transfer**: `scp user@server:/tmp/clipboard_file ./`
- **SSH clipboard forwarding**: `cat file | ssh local-machine pbcopy`
- **Pastebin sharing**: `curl -F 'f:1=<file' ix.io` for public sharing
- **Local viewing**: Direct file access for copy/paste

### Remote Development Workflow
Perfect for developers working on remote servers, containers, or headless systems where traditional clipboard tools don't work.

The skill automatically detects your platform and uses the appropriate clipboard command:

### Platform Detection & Clipboard Commands
```bash
# macOS
echo "command" | pbcopy

# Linux (X11)
echo "command" | xclip -selection clipboard

# Linux (Wayland)
echo "command" | wl-copy

# Windows/PowerShell
echo "command" | clip

# WSL
echo "command" | clip.exe

# Termux (Android)
echo "command" | termux-clipboard-set
```

### Natural Language Processing

Intelligent command generation from natural language:

- **Clean formatting focus** — Generates commands without markdown formatting or line breaks that break when pasted
- **Context-aware** — Understands intent from conversational descriptions  
- **Command optimization** — Chains operations intelligently with `&&` and `;`
- **Cross-platform aware** — Adapts commands for the target platform
- **Bypasses Claude Code limitations** — Provides clean commands when Claude Code would break them with formatting

**Example patterns:**
- "configure OpenCode to only use Copilot" → `mkdir -p ~/.config/opencode && echo '{"$schema":"https://opencode.ai/config.json","enabled_providers":["github-copilot"],"provider_lock":true}' > ~/.config/opencode/opencode.json`
- "kill SSH agent and restart it" → `pkill ssh-agent; eval $(ssh-agent -s); ssh-add ~/.ssh/id_rsa`
- "find files larger than 100MB" → `find . -size +100M -type f -exec ls -lh {} \;`
- "restart stuck Docker daemon" → `sudo systemctl restart docker || sudo service docker restart`
- "compress directory excluding dependencies" → `tar --exclude='node_modules' --exclude='vendor' -czf backup-$(date +%Y%m%d).tar.gz .`

## Security Validation

The skill warns about potentially dangerous commands and requires confirmation:

**Dangerous Patterns Detected:**
- `rm -rf /` or variations
- `sudo rm -rf` commands
- `chmod 777` on system directories
- Commands that could delete system files
- Privileged operations without context

**Response for Dangerous Commands:**
```
⚠️  WARNING: This command could be dangerous:
rm -rf /

Are you sure? This will [explain the risk].
Use with caution: [safer alternative if available]

[Still provides the command if user confirms]
```

## Response Format Implementation

The skill will intelligently detect your platform and execute the appropriate clipboard command:

```bash
# Platform Detection Logic (Internal)
# Automatically chooses the right clipboard tool:

# macOS
echo "find src -name '*.js'" | pbcopy

# Linux with X11
echo "find src -name '*.js'" | xclip -selection clipboard

# Linux with Wayland  
echo "find src -name '*.js'" | wl-copy

# Windows PowerShell
echo "find src -name '*.js'" | clip

# WSL
echo "find src -name '*.js'" | clip.exe

# Termux (Android)
echo "find src -name '*.js'" | termux-clipboard-set

# For multi-line content - use platform-appropriate heredoc
# macOS example:
cat << 'EOF' | pbcopy
#!/bin/bash
for file in *.log; do
    gzip "$file"
done
EOF

# Linux example:
cat << 'EOF' | xclip -selection clipboard
#!/bin/bash
for file in *.log; do
    gzip "$file"
done
EOF
```

### Error Handling, Installation & Fallbacks

If primary clipboard tool isn't available, the skill automatically attempts installation then fallbacks:

1. **Linux**: Auto-install `xclip` → fallback to `xsel` → `wl-copy` → manual instructions
2. **macOS**: `pbcopy` (built-in) → manual copy instructions  
3. **Windows**: `clip` (built-in) → PowerShell alternative → manual instructions
4. **Always displays content** even if clipboard operation fails

#### Auto-Installation Logic:
- **Linux (apt)**: `sudo apt-get update && sudo apt-get install -y xclip`
- **Linux (yum)**: `sudo yum install -y xclip`
- **Linux (pacman)**: `sudo pacman -S --noconfirm xclip`
- **Linux (dnf)**: `sudo dnf install -y xclip`
- **macOS (Homebrew)**: Install `pbcopy` alternatives if needed (e.g., `brew install clipboard`)
- **Windows (Chocolatey)**: `choco install clipboard` (if needed)

## Guidelines

1. **Command line requests**: Provide the exact command
2. **Code snippets**: Minimal, functional code only
3. **Configuration**: Clean config without comments unless essential
4. **Text patterns**: Raw regex, SQL, etc. without wrapper quotes unless needed
5. **Scripts**: Functional script without verbose comments

## Cross-Platform Examples

**User:** `/clipboard one-liner to configure OpenCode for Copilot only`

**macOS Response:**
```bash
echo "mkdir -p ~/.config/opencode && echo '{\"\\$schema\":\"https://opencode.ai/config.json\",\"enabled_providers\":[\"github-copilot\"],\"provider_lock\":true}' > ~/.config/opencode/opencode.json" | pbcopy
```

**Linux Response (X11):**
```bash
echo "mkdir -p ~/.config/opencode && echo '{\"\\$schema\":\"https://opencode.ai/config.json\",\"enabled_providers\":[\"github-copilot\"],\"provider_lock\":true}' > ~/.config/opencode/opencode.json" | xclip -selection clipboard
```

**Windows Response:**
```bash
echo "mkdir -p ~/.config/opencode && echo '{\"\\$schema\":\"https://opencode.ai/config.json\",\"enabled_providers\":[\"github-copilot\"],\"provider_lock\":true}' > ~/.config/opencode/opencode.json" | clip
```

**User:** `/clipboard command to find and kill process holding file handle`
**Platform-agnostic response:**
```bash
cat << 'EOF' | [platform-clipboard-tool]
lsof "$1" | awk 'NR>1 {print $2}' | xargs kill -9
EOF
```

## Implementation Logic

### Natural Language Processing Flow:
1. **Parse Intent**: Understand what the user wants to accomplish
2. **Detect Command Type**: One-liner, script, configuration, or complex operation
3. **Generate Command**: Create optimized command based on intent and context
4. **Platform Adaptation**: Adjust for target platform specifics
5. **Execute with Installation & Fallback**: Auto-install → Primary → Secondary → Manual copy
6. **Always Show Output**: Even if clipboard fails

### Clipboard Tool Detection & Installation:
```bash
# Enhanced clipboard function with headless server support
smart_clipboard() {
    local content="$1"
    local success=false
    
    echo "📋 Attempting to copy to clipboard..."

    # Method 1: Platform-specific clipboard tools
    PLATFORM=$(uname -s)
    case "$PLATFORM" in
        "Darwin")
            if command -v pbcopy &> /dev/null; then
                echo "$content" | pbcopy && success=true && echo "✅ Copied using pbcopy (macOS)"
            elif command -v brew &> /dev/null; then
                brew install clipboard && echo "$content" | clipboard && success=true
            fi
            ;;
        "Linux")
            # Auto-install and use appropriate Linux clipboard tool
            if [ -n "$WAYLAND_DISPLAY" ] && command -v wl-copy &> /dev/null; then
                echo "$content" | wl-copy && success=true && echo "✅ Copied using wl-copy (Wayland)"
            elif [ -n "$DISPLAY" ] && command -v xclip &> /dev/null; then
                echo "$content" | xclip -selection clipboard && success=true && echo "✅ Copied using xclip (X11)"
            elif [ -n "$DISPLAY" ] && command -v xsel &> /dev/null; then
                echo "$content" | xsel --clipboard --input && success=true && echo "✅ Copied using xsel (X11)"
            elif [ -n "$WAYLAND_DISPLAY" ]; then
                # Auto-install wl-clipboard for Wayland
                install_package "wl-clipboard" && echo "$content" | wl-copy && success=true
            elif [ -n "$DISPLAY" ]; then
                # Auto-install xclip for X11
                install_package "xclip" && echo "$content" | xclip -selection clipboard && success=true
            fi
            ;;
        "MINGW"*|"CYGWIN"*|"MSYS"*)
            if [ -n "$WSL_DISTRO_NAME" ]; then
                echo "$content" | clip.exe && success=true && echo "✅ Copied using clip.exe (WSL)"
            else
                echo "$content" | clip && success=true && echo "✅ Copied using clip (Windows)"
            fi
            ;;
    esac

    # Method 2: OSC 52 escape sequences for remote terminals (headless support)
    if [ "$success" = false ] && [ -t 1 ]; then
        local encoded=$(echo -n "$content" | base64 | tr -d '\n')
        printf '\033]52;c;%s\a' "$encoded"
        echo "✅ Sent to clipboard via OSC 52 (works in modern terminals like iTerm2, Alacritty, etc.)"
        success=true
    fi

    # Method 3: tmux integration
    if [ "$success" = false ] && command -v tmux >/dev/null 2>&1 && [ -n "$TMUX" ]; then
        echo "$content" | tmux load-buffer -
        echo "✅ Copied to tmux buffer (use 'tmux show-buffer' to retrieve)"
        success=true
    fi

    # Method 4: Fallback for completely headless environments
    if [ "$success" = false ]; then
        local temp_file="/tmp/clipboard_$(date +%s).txt"
        echo "$content" > "$temp_file"
        chmod 644 "$temp_file"
        echo "📁 Saved to: $temp_file"
        echo ""
        echo "💡 Manual copy options for headless servers:"
        echo "   • scp $(whoami)@$(hostname):$temp_file ./"
        echo "   • cat $temp_file | ssh local-machine pbcopy"
        echo "   • curl -F 'f:1=<$temp_file' ix.io  # Share via pastebin"
        echo "   • cat $temp_file  # View and copy manually"
    fi

    # Always display content for verification
    echo ""
    echo "📄 Content copied:"
    echo "===================="
    echo "$content"
    echo "===================="
}

# Package installation helper
install_package() {
    local package="$1"
    echo "📦 Installing $package..."
    
    if command -v apt-get &> /dev/null; then
        sudo apt-get update && sudo apt-get install -y "$package"
    elif command -v yum &> /dev/null; then
        sudo yum install -y "$package"
    elif command -v pacman &> /dev/null; then
        sudo pacman -S --noconfirm "$package"
    elif command -v dnf &> /dev/null; then
        sudo dnf install -y "$package"
    else
        echo "❌ Unable to auto-install $package - no supported package manager found"
        return 1
    fi
}
```

### Command Generation:
1. **Analyze Request**: Extract action, target, and context from natural language
2. **Optimize for Platform**: Use appropriate tools and syntax for the environment
3. **Chain Operations**: Combine multiple steps with `&&` or `;` when beneficial

### Security Validation:
1. **Scan for Dangerous Patterns**: Regex matching against known dangerous commands
2. **Risk Assessment**: Evaluate potential system impact
3. **User Warning**: Display warning with explanation and alternatives
4. **Confirmation Required**: User must explicitly confirm dangerous operations

---

**CRITICAL IMPLEMENTATION NOTE:** 
Always detect the user's platform and environment (GUI, headless, SSH, tmux) at runtime and use the most appropriate clipboard method. The skill must gracefully handle all environments from GUI desktops to headless servers by automatically trying multiple methods in order of preference. After successfully copying to clipboard, ALWAYS display what was copied to confirm the operation succeeded.

**WORKFLOW:**
1. Detect platform, display server, and environment context
2. Try platform-specific clipboard tools (with auto-installation if needed)
3. Fallback to OSC 52 escape sequences for terminal clipboard access
4. Fallback to tmux buffers if in tmux session
5. Final fallback to temporary file with multiple manual copy options
6. Always display copied content for verification and manual copying if needed

**PRIORITY ORDER:**
1. **Native clipboard tools**: `pbcopy` (macOS), `xclip`/`wl-copy` (Linux), `clip.exe` (Windows)
2. **OSC 52 terminal clipboard**: For SSH sessions and headless environments
3. **Tmux buffers**: When running inside tmux
4. **File sharing**: Temporary files with SCP/pastebin instructions

**HEADLESS SERVER SUPPORT:**
- **OSC 52**: `printf '\033]52;c;%s\a' "$(echo -n "$content" | base64)"`
- **Tmux**: `echo "$content" | tmux load-buffer -`
- **File sharing**: Create temp files with transfer instructions

**AUTO-INSTALLATION:**
- Detect package manager and auto-install missing clipboard tools
- Support apt, yum, dnf, pacman for Linux distributions
- Use Homebrew alternatives for macOS if needed