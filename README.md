<div align="center">

# 🩺 Code Doctor

**An intelligent code review assistant for Claude Code** — combining automated static analysis with structured manual checklists to catch issues before they reach production.

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/adityachaudhari/Claude-Doctor)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-2.1%2B-orange.svg)](https://claude.com/claude-code)
[![Status](https://img.shields.io/badge/status-active-brightgreen.svg)]()

</div>

---

## 🎯 Why Code Doctor Exists

Code review is one of the most critical — yet inconsistent — parts of software development. Every team has different standards, reviewers miss different things, and automated tools often produce noise without context.

**Code Doctor solves this by combining two approaches:**

| Approach | What It Catches | Limitation |
|----------|-----------------|------------|
| **🔍 Automated Scanner** | Secrets, SQL injection risks, XSS vectors, debug code, empty catch blocks, console logs, TODO/FIXME, hardcoded credentials | Pattern-based — can't understand intent |
| **📋 Manual Checklist** | Architecture decisions, business logic correctness, API design, documentation completeness, team conventions | Human-dependent — easy to skip |

**Together, they provide defense in depth.** The scanner catches the mechanical errors humans miss; the checklist ensures the thoughtful review humans excel at.

---

## ✨ Features

### 🔍 Automated Code Scanner (27 Patterns)
| Severity | Categories | Examples |
|----------|------------|----------|
| 🔴 **Critical** | Secrets, Injection, Code Execution | Hardcoded passwords, API keys, AWS credentials, private keys, `eval()`, `new Function()`, SQL injection patterns |
| 🟡 **Warning** | Security, Error Handling, Debug Code | `innerHTML` XSS risks, empty catch blocks, `debugger` statements, SQL concatenation, `FIXME`/`HACK` comments |
| 🔵 **Info** | Hygiene, Performance, Maintenance | `console.log`/`print` statements, `TODO` comments, loop performance hints, `fmt.Print` |

### 📋 Structured Manual Checklist (32 Items)
| Category | Items | Focus |
|----------|-------|-------|
| ✨ **General** | 8 | Clean code, error handling, logging, comments, naming, tests, docs, no console |
| 🔒 **Security** | 8 | Input validation, no secrets, SQL injection prevention, XSS prevention, auth, crypto, permissions, rate limiting |
| ⚡ **Performance** | 8 | N+1 queries, indexes, caching, memory leaks, bundle size, lazy loading, algorithms, pagination |
| 📋 **Project-Specific** | 8 | Architecture, forbidden patterns, required files, naming conventions, dependencies, breaking changes, migrations, feature flags |

### 🎨 User Experience
- **Visual pane** — opens with `/code-doctor` command
- **Context menu** — right-click anywhere → "Open Code Doctor"
- **Status line** — shows 🩺 when ready
- **Persistent state** — scan results survive restarts
- **Filterable results** — toggle between Critical/Warning/Info/All
- **Code snippets** — see the exact line that triggered each issue

---

## 🚀 Installation

### Prerequisites
- **Claude Code** ≥ 2.1.275
- **Git** (for cloning)

### Quick Install (Recommended)

```bash
# Clone to Claude's plugin directory
git clone https://github.com/adityachaudhari/Claude-Doctor.git ~/.claude/dev-mods/code-doctor

# Restart Claude Code (or reload plugins)
claude --plugin-dir ~/.claude/dev-mods/code-doctor
```

### Manual Install

```bash
# Create plugin directory
mkdir -p ~/.claude/dev-mods/code-doctor

# Copy these files into it:
# ├── .claude-plugin/plugin.json
# ├── hooks/hooks.json
# ├── hooks/register.tsx
# ├── icon.png
# ├── LICENSE
# └── README.md

# Load plugin
claude --plugin-dir ~/.claude/dev-mods/code-doctor
```

### Verify Installation

```bash
# Validate plugin structure
claude plugin validate ~/.claude/dev-mods/code-doctor

# In Claude Code, run:
/code-doctor
```

**Success indicators:**
- ✅ Pane opens titled "Code Doctor 🩺"
- ✅ Status line shows `🩺 Code Doctor ready`
- ✅ Right-click → "Open Code Doctor" works
- ✅ "🔍 Scan Repository" button triggers scan

---

## 🎮 Usage

### Running a Scan

1. Open the panel: `/code-doctor`
2. Click **"🔍 Scan Repository"**
3. Wait for toast: `Scan complete: X issues in Y files (Zms)`
4. Filter results using dropdown: **All / Critical / Warning / Info**
5. Review each issue with file path, line number, and code snippet

### Manual Review

Below the scanner, work through the **32-item checklist**:
- Click through each category
- Mentally verify each item
- Use "Mark Review Complete" when done

### Keyboard Shortcuts

| Action | Command |
|--------|---------|
| Open panel | `/code-doctor` |
| Quick scan | Right-click → "Open Code Doctor" |

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Code Doctor Plugin                        │
├─────────────────────────────────────────────────────────────┤
│  Commands                  │  UI Renderers                   │
│  ─────────────             │  ───────────                   │
│  /code-doctor              │  Pane (main interface)          │
│  /code-doctor-scan-internal│  ContextMenu (right-click)      │
│                            │  StatusLine (🩺 indicator)       │
├─────────────────────────────────────────────────────────────┤
│  State Management ($.store)                                 │
│  ─────────────────────────                                  │
│  • code-doctor-scan     → { issues, scanning, lastScan }   │
│  • code-doctor-severity → 'all' | 'critical' | 'warning'    │
├─────────────────────────────────────────────────────────────┤
│  Scanner Engine (27 regex patterns)                         │
│  ─────────────────────────────────                          │
│  • Secrets detection (passwords, keys, tokens)              │
│  • Injection risks (SQL, XSS, code execution)               │
│  • Error handling anti-patterns                             │
│  • Debug/hygiene code                                       │
│  • Performance hints                                        │
├─────────────────────────────────────────────────────────────┤
│  File System Access ($.fs)                                  │
│  ─────────────────────────                                  │
│  • $.fs.glob() — find code files (500 max)                  │
│  • $.fs.read() — read file contents                         │
│  • Excludes: node_modules, .git, dist, build                │
└─────────────────────────────────────────────────────────────┘
```

---

## 📁 Project Structure

```
Claude-Doctor/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest (name, version, author, icon)
├── hooks/
│   ├── hooks.json           # Module registration
│   └── register.tsx         # Main plugin logic (commands, UI, scanner)
├── icon.png                 # 512×512 plugin icon
├── LICENSE                  # MIT License
└── README.md                # This file
```

---

## 🔧 Configuration

### Customize Scan Patterns

Edit `hooks/register.tsx` → `scanFile()` function → `patterns` array:

```typescript
const patterns = [
  // Add custom pattern
  { 
    regex: /your-custom-pattern/gi, 
    type: 'custom-issue', 
    severity: 'warning', 
    message: 'Your custom message' 
  },
  // ... existing patterns
]
```

### Adjust Scan Limits

```typescript
// In register.tsx, scan command:
.slice(0, 500)  // Max files to scan (change as needed)
```

### Exclude Additional Directories

```typescript
.filter(f => !f.includes('node_modules') 
          && !f.includes('.git') 
          && !f.includes('dist') 
          && !f.includes('build')
          && !f.includes('your-folder'))  // Add yours
```

---

## 🤝 Contributing

We welcome contributions! Here's how:

### Adding New Detection Patterns

1. Fork the repository
2. Add pattern to `scanFile()` in `hooks/register.tsx`
3. Test with sample code
4. Submit PR with description of what it catches

### Improving the Checklist

1. Edit `categories` array in `register.tsx`
2. Follow existing format: `{ id, label }`
3. Keep items specific and actionable

### Reporting Issues

- **False positives** → Open issue with code snippet
- **Missed patterns** → Provide example code
- **UI bugs** → Screenshot + steps to reproduce

---

## 📜 License

MIT License — see [LICENSE](LICENSE) for details.

```
Copyright (c) 2024 Aditya Chaudhari

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software...
```

---

## 👨‍💻 Author

### **Aditya Chaudhari**

**Full Stack Developer & Security Enthusiast**

- **GitHub:** [@adityachaudhari](https://github.com/adityachaudhari)
- **LinkedIn:** [Aditya Chaudhari](https://linkedin.com/in/adityachaudhari)
- **Portfolio:** [adityachaudhari.dev](https://adityachaudhari.dev)

### 🎯 Contributions to This Project

| Area | Contribution |
|------|--------------|
| **Concept & Design** | Designed the hybrid scanner + checklist approach for defense-in-depth code review |
| **Plugin Architecture** | Built on Claude Code's plugin system using `$.ui`, `$.store`, `$.fs`, `$.command` APIs |
| **Scanner Engine** | Implemented 27 detection patterns covering secrets, injection, XSS, error handling, debug code, performance |
| **UI/UX** | Created intuitive pane interface with severity filtering, code snippets, context menu integration |
| **Persistence** | Used `$.store` for cross-session state (scan results survive restarts) |
| **Distribution** | Packaged as installable plugin with validation, README, and MIT license |

### 💡 Philosophy

> *"Code review shouldn't be a checkbox exercise. It should be a systematic process where automation handles the mechanical checks, freeing humans to focus on the thoughtful review only they can do."*

---

## 🙏 Acknowledgments

- **Claude Code Team** — for the extensible plugin platform
- **Security Researchers** — whose public work informed detection patterns
- **Open Source Community** — for the tools and patterns that made this possible

---

## 📊 Stats

![Lines of Code](https://img.shields.io/badge/lines%20of%20code-~600-blue)
![Detection Patterns](https://img.shields.io/badge/detection%20patterns-27-green)
![Checklist Items](https://img.shields.io/badge/checklist%20items-32-orange)
![Languages Supported](https://img.shields.io/badge/languages-15+-purple)

---

<div align="center">

**Made with ❤️ for developers who take code quality seriously**

[⭐ Star this repo](https://github.com/adityachaudhari/Claude-Doctor) • [🐛 Report Issue](https://github.com/adityachaudhari/Claude-Doctor/issues) • [💡 Request Feature](https://github.com/adityachaudhari/Claude-Doctor/issues/new)

</div>
