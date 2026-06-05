---
name: from-clipboard
description: >
  This skill should be used when the user asks to "read from clipboard", "get clipboard content", 
  mentions "/from-clipboard", wants to process text/code that's currently in the clipboard,
  or needs to analyze/modify content that's already copied. Also triggered by requests to 
  paste, retrieve, or access clipboard contents.
version: 1.0.0
---

# From Clipboard Skill

**Slash command:** `/from-clipboard`

**Description:** Read and process content from the system clipboard. Perfect for analyzing code, modifying text, debugging copied content, or processing data that's already in your clipboard.

## Platform Support

- **macOS**: `pbpaste`
- **Linux**: `xclip` / `xsel` / `wl-paste` (Wayland)
- **Windows**: PowerShell `Get-Clipboard`
- **WSL**: `powershell.exe -command "Get-Clipboard"`
- **Termux**: `termux-clipboard-get`
- **Headless Servers**: tmux buffers, OSC 52 (read capability varies by terminal)

## Usage

```bash
/from-clipboard analyze this code
/from-clipboard fix the bugs in this code
/from-clipboard explain what this does
/from-clipboard convert this to TypeScript
/from-clipboard format this JSON
/from-clipboard review this code for security issues
```

## Behavior

- **Read from clipboard** — Retrieve current clipboard content
- **Process content** — Analyze, modify, explain, or transform the retrieved text
- **Display original** — Show what was read from clipboard for verification
- **Platform detection** — Automatically use appropriate clipboard tool
- **Error handling** — Graceful fallbacks when clipboard tools unavailable
- **Content validation** — Warn if clipboard is empty or contains unexpected data types

## Use Cases

### Code Analysis
```bash
/from-clipboard analyze this code for performance issues
# → Reads clipboard code, analyzes it, provides optimization suggestions

/from-clipboard explain this function
# → Reads clipboard code, explains what it does step by step

/from-clipboard find bugs in this code
# → Reads clipboard code, identifies potential bugs and fixes
```

### Code Transformation
```bash
/from-clipboard convert this JavaScript to TypeScript
# → Reads JS from clipboard, converts to TypeScript

/from-clipboard add error handling to this code
# → Reads clipboard code, adds try-catch blocks and error handling

/from-clipboard refactor this to use async/await
# → Reads callback-based code, converts to async/await pattern
```

### Data Processing
```bash
/from-clipboard format this JSON
# → Reads clipboard JSON, pretty-prints and validates it

/from-clipboard parse this CSV and show summary
# → Reads CSV data, analyzes and summarizes

/from-clipboard extract URLs from this text
# → Reads clipboard text, extracts all URLs
```

### Debugging
```bash
/from-clipboard why is this error happening
# → Reads error message/stack trace, explains root cause

/from-clipboard fix this configuration
# → Reads config file content, identifies and fixes issues
```

## Platform Detection & Clipboard Commands

```bash
# macOS
pbpaste

# Linux (X11)
xclip -selection clipboard -o

# Linux (Wayland)
wl-paste

# Windows PowerShell
powershell.exe -command "Get-Clipboard"

# WSL
powershell.exe -command "Get-Clipboard"

# Termux (Android)
termux-clipboard-get
```

## Response Format

1. **Display what was read** — Show clipboard content for verification
2. **Process the content** — Perform the requested operation (analyze, fix, convert, etc.)
3. **Provide result** — Show the processed output

**Example Response:**
```
📋 Clipboard content:
====================
function oldWay(callback) {
  setTimeout(() => {
    callback(data);
  }, 1000);
}
====================

✅ Converting to async/await...

async function newWay() {
  await new Promise(resolve => setTimeout(resolve, 1000));
  return data;
}
```

## Error Handling

If clipboard tools are unavailable:
- **tmux**: Try reading from tmux buffer
- **File fallback**: Prompt user to save clipboard to file for processing
- **Manual input**: Offer alternative to paste content directly

## Security Considerations

- **Warning for sensitive data** — Alert if clipboard contains API keys, passwords, or secrets
- **Sanitization** — Option to redact sensitive information before processing
- **Confirmation** — Ask before processing content that looks like credentials

## Implementation Logic

### Clipboard Reading Flow:
1. **Detect Platform** — Identify OS and available clipboard tools
2. **Read Content** — Use appropriate command to retrieve clipboard
3. **Validate Content** — Check if content is empty or unexpected type
4. **Display Original** — Show what was read for verification
5. **Process Request** — Apply user's requested transformation/analysis
6. **Return Result** — Provide processed output

### Platform-Specific Reading:
```bash
# Enhanced clipboard reading function
read_clipboard() {
    local content=""
    local success=false
    
    echo "📋 Reading from clipboard..."

    PLATFORM=$(uname -s)
    case "$PLATFORM" in
        "Darwin")
            if command -v pbpaste &> /dev/null; then
                content=$(pbpaste) && success=true
            fi
            ;;
        "Linux")
            if [ -n "$WAYLAND_DISPLAY" ] && command -v wl-paste &> /dev/null; then
                content=$(wl-paste) && success=true
            elif [ -n "$DISPLAY" ] && command -v xclip &> /dev/null; then
                content=$(xclip -selection clipboard -o) && success=true
            elif [ -n "$DISPLAY" ] && command -v xsel &> /dev/null; then
                content=$(xsel --clipboard --output) && success=true
            fi
            ;;
        "MINGW"*|"CYGWIN"*|"MSYS"*)
            if [ -n "$WSL_DISTRO_NAME" ]; then
                content=$(powershell.exe -command "Get-Clipboard") && success=true
            else
                content=$(powershell.exe -command "Get-Clipboard") && success=true
            fi
            ;;
    esac

    # Tmux fallback
    if [ "$success" = false ] && command -v tmux >/dev/null 2>&1 && [ -n "$TMUX" ]; then
        content=$(tmux show-buffer) && success=true
    fi

    if [ "$success" = false ]; then
        echo "❌ Unable to read from clipboard"
        echo "💡 Alternative: Paste the content directly or save to a file"
        return 1
    fi

    echo "$content"
}
```

### Content Processing:
- **Code analysis** — Parse, analyze, and provide insights
- **Transformation** — Convert between formats/languages
- **Debugging** — Identify issues and suggest fixes
- **Documentation** — Generate docs from code
- **Refactoring** — Improve code structure and quality

---

**CRITICAL IMPLEMENTATION NOTE:**
Always read and display the clipboard content first for verification before processing. This ensures the user knows what content is being analyzed or modified. Handle empty clipboard gracefully with helpful error messages and alternatives.

**WORKFLOW:**
1. Detect platform and available clipboard tools
2. Read clipboard content using appropriate method
3. Display original content for verification
4. Process content according to user's request
5. Provide result with clear before/after comparison when applicable