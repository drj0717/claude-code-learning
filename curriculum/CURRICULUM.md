# Claude Code Mastery Curriculum

> Sensei-guided learning path for mastering Claude Code CLI
> Student: Phoenix | Started: 2026-02-14

---

## Module 1: Foundations & Mental Model
*Understand what Claude Code is, how it thinks, and why it works the way it does.*

- [ ] **1.1 What is Claude Code?**
  - CLI tool vs Desktop App vs API — how they relate
  - Claude Code as an agentic coding assistant (not just a chatbot)
  - The conversation loop: prompt → think → tool use → respond
  - Context window: what it is, why it matters, how compression works

- [ ] **1.2 How Claude Code "Thinks"**
  - The tool-use model: Claude doesn't execute code — it calls tools
  - Available tools: Read, Write, Edit, Glob, Grep, Bash, WebSearch, WebFetch, Task (agents)
  - Permission model: auto-allow, prompt, deny — and why it exists
  - How Claude decides which tool to use and when

- [ ] **1.3 The Context Window**
  - Token limits and what counts toward them
  - How conversation history is managed and compressed
  - Why shorter, focused sessions often beat marathon ones
  - The /clear command and when to use it
  - How CLAUDE.md content is always loaded (system prompt injection)

- [ ] **1.4 Your Environment: WSL + Windows**
  - How Claude Code runs inside WSL
  - File system differences (Linux vs Windows paths)
  - Interop considerations (accessing Windows files from WSL)
  - Terminal choices: Windows Terminal, VS Code integrated terminal

---

## Module 2: Setup & Configuration
*Configure Claude Code to work the way you want it to.*

- [ ] **2.1 Installation & Updates**
  - Installing via npm: `npm install -g @anthropic-ai/claude-code`
  - Updating to latest version
  - Checking version: `claude --version`
  - Authentication and API key management

- [ ] **2.2 CLAUDE.md — Your Personal Instructions**
  - What CLAUDE.md is and where it lives (~/.claude/CLAUDE.md, project-level, folder-level)
  - Hierarchy and precedence of CLAUDE.md files
  - What to put in it: coding style, preferences, project context
  - What NOT to put in it (don't duplicate what Claude already knows)
  - Hands-on: Build your personal CLAUDE.md

- [ ] **2.3 Settings & Permissions**
  - Settings file locations and structure
  - Permission modes: default, permissive, restricted
  - Auto-allow rules for tools and commands
  - The `--dangerously-skip-permissions` flag (and why to avoid it)

- [ ] **2.4 Keybindings**
  - Default keybindings
  - Customizing ~/.claude/keybindings.json
  - Chord bindings for power users
  - Changing the submit key (Enter vs Ctrl+Enter)

- [ ] **2.5 Model Selection**
  - Available models: Opus, Sonnet, Haiku
  - When to use which model
  - /model command to switch mid-conversation
  - Cost and speed tradeoffs

---

## Module 3: Core Commands & Daily Workflow
*The tools and commands you'll use every day.*

- [ ] **3.1 Slash Commands**
  - /help — get help
  - /clear — reset conversation context
  - /compact — compress conversation to save context
  - /model — switch models
  - /cost — check token usage
  - /config — manage settings
  - /memory — manage memory files
  - /status — check status
  - /review-pr — review pull requests
  - /commit — create git commits
  - Custom skills and slash commands

- [ ] **3.2 File Operations**
  - Read: viewing files (supports images, PDFs, notebooks)
  - Write: creating new files
  - Edit: precise string replacement in existing files
  - Glob: finding files by pattern (replaces `find`)
  - Grep: searching file contents (replaces `grep`/`rg`)
  - When to use which tool

- [ ] **3.3 Bash Tool — Shell Commands**
  - When Bash is appropriate vs dedicated tools
  - Running builds, tests, servers
  - Background processes with run_in_background
  - Timeout handling
  - Git operations through Bash

- [ ] **3.4 Web Tools**
  - WebSearch: searching the internet for current info
  - WebFetch: fetching and analyzing web pages
  - Limitations and when they fail (auth-required pages)

- [ ] **3.5 Navigation Patterns**
  - Exploring an unfamiliar codebase
  - Finding where things are defined
  - Understanding code flow and architecture
  - Reading error messages and stack traces

---

## Module 4: Prompting Techniques
*How to communicate with Claude Code for the best results.*

- [ ] **4.1 The Art of the Prompt**
  - Be specific about what you want (outcome, not steps)
  - Provide context: why, not just what
  - Reference files and line numbers when possible
  - One task per message vs compound tasks

- [ ] **4.2 Scoping Work**
  - Small, focused tasks get better results
  - Breaking large features into steps
  - When to start fresh (/clear) vs continue
  - Using plan mode for complex tasks

- [ ] **4.3 Giving Feedback**
  - How to correct mistakes effectively
  - "Undo that" and reverting changes
  - Saying "no" and redirecting
  - Building on partial results

- [ ] **4.4 Power Prompting Patterns**
  - "Read X before changing it" — forcing context gathering
  - "Explain your approach before coding" — getting alignment
  - "Only change what I asked for" — preventing scope creep
  - "Show me the diff" — reviewing before committing
  - Using constraints: "Do not modify file Y"
  - Multi-step instructions with numbered lists

- [ ] **4.5 Anti-Patterns to Avoid**
  - Vague prompts: "make it better"
  - Overloading: asking for 10 things at once
  - Not reading Claude's output before asking for more
  - Fighting the tool instead of adapting your approach

---

## Module 5: Plan Mode & Task Management
*Thinking before coding — how to plan and track complex work.*

- [ ] **5.1 Plan Mode**
  - What plan mode is and when to use it
  - How Claude explores before proposing
  - Reviewing and approving plans
  - Modifying plans before execution

- [ ] **5.2 Task Lists (TodoWrite)**
  - Creating structured task lists
  - Tracking progress on multi-step work
  - Dependencies between tasks
  - When task lists help vs when they're overhead

- [ ] **5.3 Multi-Step Workflows**
  - Breaking features into atomic changes
  - Commit-per-step workflow
  - Testing between steps
  - Recovery when things go wrong

---

## Module 6: Git & GitHub Mastery
*Version control workflows powered by Claude Code.*

- [ ] **6.1 Git Fundamentals with Claude**
  - How Claude interacts with git
  - The /commit skill — smart commit messages
  - Staging, committing, and the safety protocol
  - Claude never pushes unless you ask

- [ ] **6.2 GitHub Integration**
  - Using `gh` CLI through Claude (issues, PRs, releases)
  - Creating pull requests with Claude
  - Reviewing PRs: /review-pr
  - Reading PR comments and responding
  - Working with GitHub Actions output

- [ ] **6.3 Branching Workflows**
  - Feature branch workflow with Claude
  - Handling merge conflicts
  - Rebasing vs merging — when Claude can help
  - Protecting main/master from accidents

- [ ] **6.4 Repository Management**
  - Setting up new repos
  - Managing .gitignore
  - Project-level CLAUDE.md for team repos
  - Code review best practices with AI

---

## Module 7: Advanced Features
*Power-user capabilities for maximum productivity.*

- [ ] **7.1 Multi-Agent Architecture (Task Tool)**
  - What agents are and when to use them
  - Agent types: Bash, Explore, Plan, general-purpose
  - Running agents in background
  - Resuming agents
  - Parallel agent execution

- [ ] **7.2 Hooks**
  - What hooks are (shell commands triggered by events)
  - Pre/post tool execution hooks
  - Prompt submit hooks
  - Auto-formatting, linting, testing on save
  - Custom workflow automation

- [ ] **7.3 MCP Servers (Model Context Protocol)**
  - What MCP is and why it matters
  - Connecting external tools to Claude Code
  - Available MCP servers (filesystem, GitHub, databases, etc.)
  - Setting up and configuring MCP servers
  - Building custom MCP servers

- [ ] **7.4 Memory System**
  - Auto-memory: how Claude remembers across sessions
  - MEMORY.md and topic files
  - /memory command
  - What to save vs what not to save
  - Project-specific vs global memory

- [ ] **7.5 Custom Skills**
  - What skills are
  - Built-in skills (/commit, /review-pr, keybindings-help)
  - Creating custom slash commands
  - Sharing skills across projects

---

## Module 8: IDE Integration
*Using Claude Code inside your editors.*

- [ ] **8.1 VS Code Integration**
  - Claude Code extension for VS Code
  - Using the integrated terminal
  - Side-by-side coding with Claude
  - Inline suggestions vs CLI workflow

- [ ] **8.2 Windsurf Considerations**
  - Windsurf's AI features vs Claude Code CLI
  - Using them together vs separately
  - When to use which tool
  - Avoiding conflicts between AI assistants

- [ ] **8.3 Claude Desktop App**
  - Desktop app vs CLI: different tools for different jobs
  - When to use Desktop (research, writing, analysis)
  - When to use CLI (coding, file ops, git)
  - Sharing context between them (copy/paste strategies)

---

## Module 9: External Tool Integration
*Connecting Claude Code to your broader toolkit.*

- [ ] **9.1 Cloudflare**
  - Deploying sites with Wrangler CLI through Claude
  - Cloudflare Pages workflow
  - Workers and serverless functions
  - DNS and domain management
  - Cloudflare API via Claude

- [ ] **9.2 Microsoft 365 Integration**
  - Limitations: Claude can't directly access M365
  - Workarounds: export/import workflows
  - Using Claude for document drafting (then paste to Word/Outlook)
  - PowerShell/Graph API for automation

- [ ] **9.3 Docker & Containers (WSL)**
  - Docker in WSL2
  - Using Claude to write Dockerfiles
  - Container management through Bash tool
  - Development environments

- [ ] **9.4 Package Managers & Build Tools**
  - npm/yarn/pnpm/bun
  - pip/poetry/uv for Python
  - cargo for Rust
  - Claude's awareness of lock files and dependencies

- [ ] **9.5 Databases**
  - SQLite, PostgreSQL, MySQL through CLI
  - MCP servers for database access
  - Schema management and migrations
  - Claude writing queries safely

---

## Module 10: Real-World Projects
*Apply everything by building real things.*

- [ ] **10.1 Project: Personal CLI Tool**
  - Build a CLI tool from scratch with Claude
  - Practice: plan mode, task lists, incremental commits
  - Tech: Node.js or Python

- [ ] **10.2 Project: Full-Stack Web App**
  - Build and deploy a web app
  - Practice: Cloudflare Pages deployment, GitHub workflow
  - Tech: Your choice of framework

- [ ] **10.3 Project: Automation Scripts**
  - Automate repetitive tasks in your workflow
  - Practice: hooks, MCP servers, Bash scripting
  - Tech: Shell scripts, Python, or Node.js

- [ ] **10.4 Project: Open Source Contribution**
  - Find, understand, and contribute to an open source project
  - Practice: codebase exploration, PR workflow, code review
  - The full cycle: fork → explore → fix → PR

---

## Bonus Modules

- [ ] **B.1 Debugging with Claude**
  - Reading error messages and logs
  - Systematic debugging approach
  - Using Claude to trace through code
  - Common pitfalls and how Claude helps

- [ ] **B.2 Security Awareness**
  - What Claude won't do (and why)
  - Keeping secrets out of commits
  - Reviewing code for vulnerabilities
  - Safe handling of .env files and credentials

- [ ] **B.3 Cost Optimization**
  - Understanding token usage and costs
  - Using /cost to monitor spending
  - Haiku for simple tasks, Opus for complex ones
  - /compact to extend conversations cheaply

- [ ] **B.4 Team Collaboration**
  - Shared CLAUDE.md for team standards
  - PR review workflows
  - Onboarding new team members with Claude
  - Consistent code style across a team

---

## Progress Legend
- [ ] Not started
- [~] In progress
- [x] Completed
- [!] Needs review/revisit
