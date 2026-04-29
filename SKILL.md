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

**Description:** Clean, optimized command line or text output directly to clipboard without explanations. Works across all platforms.

## Platform Support

- **macOS**: `pbcopy` / `pbpaste`
- **Linux**: `xclip` / `xsel` / `wl-copy` (Wayland)
- **Windows**: `clip.exe` / PowerShell
- **WSL**: `clip.exe`
- **Termux**: `termux-clipboard-set`

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
- **One-liner focus** — Optimized for sharing unusual, hard-to-find commands with colleagues
- **Copy to clipboard then display** — Execute clipboard command then show what was copied
- **NO "here's the command" or similar preamble before copying**
- **Show copied content after clipboard operation** — Confirm what was successfully copied
- **Optimized for command sharing** — Perfect for Slack, Discord, or any communication platform
- **Auto-detects platform and uses appropriate clipboard tool**
- **Single line preferred when possible**
- **Chain commands intelligently** (use `&&` or `;` when beneficial)
- **Multiple lines only when necessary for readability**
- **Security validation for dangerous commands** (warns with confirmation)
- **Context-aware command optimization**

## Cross-Platform Clipboard Implementation

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

- **One-liner focus** — Specializes in creating commands perfect for sharing with colleagues
- **Context-aware** — Understands intent from conversational descriptions  
- **Command optimization** — Chains operations intelligently with `&&` and `;`
- **Cross-platform aware** — Adapts commands for the target platform

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

### Error Handling & Fallbacks

If primary clipboard tool isn't available, the skill attempts fallbacks:

1. **Linux**: `xclip` → `xsel` → `wl-copy` → manual instructions
2. **macOS**: `pbcopy` → manual copy instructions  
3. **Windows**: `clip` → PowerShell alternative → manual instructions
4. **Always displays content** even if clipboard operation fails

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
5. **Execute with Fallback**: Primary → Secondary → Manual copy
6. **Always Show Output**: Even if clipboard fails

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
Always detect the user's platform at runtime and use the appropriate clipboard command. The skill must work identically across all platforms while using platform-specific clipboard tools. After successfully copying to clipboard, ALWAYS display what was copied to confirm the operation succeeded.

**WORKFLOW:**
1. Generate the command/text/code based on user request
2. Copy it to clipboard using platform-appropriate tool  
3. Display the copied content as confirmation