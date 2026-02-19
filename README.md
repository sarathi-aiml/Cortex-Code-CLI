# Cortex Code CLI - The Complete Guide

> **From Zero to Hero: Everything You Need to Know About Cortex Code**

Cortex Code (CoCo) is Snowflake's AI-powered coding assistant that runs in your terminal. Whether you're a complete beginner who has never used a command line, or an expert looking to maximize productivity, this guide has you covered.

---

## 📚 Table of Contents

### Part 1: Foundations (Beginners)
1. [What is Cortex Code?](#what-is-cortex-code)
2. [Understanding the Command Line](#understanding-the-command-line)
3. [Installation & First Run](#installation--first-run)
4. [Your First Conversation](#your-first-conversation)
5. [Basic Operations](#basic-operations)

### Part 2: Core Features (Intermediate)
6. [Slash Commands - Complete Reference](#slash-commands---complete-reference)
7. [Special Syntax & Shortcuts](#special-syntax--shortcuts)
8. [Working with Snowflake](#working-with-snowflake)
9. [Session Management](#session-management)
10. [File Operations](#file-operations)

### Part 3: Advanced Features (Expert)
11. [CLI Subcommands Deep Dive](#cli-subcommands-deep-dive)
12. [Skills System](#skills-system)
13. [Agents & Subagents](#agents--subagents)
14. [Hooks System](#hooks-system)
15. [MCP Integration](#mcp-integration)
16. [Browser Automation](#browser-automation)
17. [Profiles](#profiles)

### Part 4: Mastery
18. [Configuration Deep Dive](#configuration-deep-dive)
19. [Custom Development](#custom-development)
20. [Performance Optimization](#performance-optimization)
21. [Security Best Practices](#security-best-practices)
22. [Troubleshooting](#troubleshooting)
23. [Real-World Workflows](#real-world-workflows)
24. [Quick Reference](#quick-reference)

---

# Part 1: Foundations (Beginners)

---

## What is Cortex Code?

### The Simple Explanation

Imagine having an expert developer sitting next to you 24/7 who can:
- Read and understand your code instantly
- Write new code for you
- Fix bugs
- Run commands on your computer
- Query your databases
- Explain anything you don't understand

That's Cortex Code. You just talk to it in plain English, and it does the work.

### What Makes It Special?

| Feature | What It Means |
|---------|---------------|
| **AI-Powered** | Uses advanced AI (Claude) to understand context and write code |
| **Snowflake Native** | Built specifically to work with Snowflake databases |
| **Terminal-Based** | Runs in your command line for maximum speed and integration |
| **Context-Aware** | Understands your entire project, not just snippets |
| **Tool-Enabled** | Can actually execute commands, not just suggest them |

### Who Is This For?

| User Type | How Cortex Code Helps |
|-----------|----------------------|
| **Complete Beginners** | Learn by doing - ask questions, see real code |
| **Students** | Understand code, get homework help, learn best practices |
| **Data Analysts** | Write SQL queries, analyze data, create reports |
| **Developers** | Speed up coding, debug faster, automate tasks |
| **Data Engineers** | Build pipelines, manage Snowflake, create dbt projects |
| **DevOps/Platform** | Automate infrastructure, write scripts, manage deployments |

---

## Understanding the Command Line

> **If you've never used a terminal before, start here.**

### What is a Terminal?

A terminal (also called command line, console, or shell) is a text-based interface to your computer. Instead of clicking icons and buttons, you type commands.

**Think of it like texting your computer.** You type a command, press Enter, and the computer responds.

### Why Use a Terminal?

| Reason | Explanation |
|--------|-------------|
| **Speed** | Typing is faster than clicking through menus |
| **Power** | Some things can only be done via terminal |
| **Automation** | You can save and repeat commands |
| **Remote Access** | Control computers without a screen |
| **Professional Standard** | All developers use terminals |

### Opening Your Terminal

#### On Mac
1. Press `Cmd + Space` (opens Spotlight search)
2. Type `Terminal`
3. Press `Enter`

**Alternative:** Go to Applications → Utilities → Terminal

#### On Windows
1. Press `Win + R` (opens Run dialog)
2. Type `cmd` or `powershell`
3. Press `Enter`

**Alternative:** Search for "Command Prompt" or "PowerShell" in Start menu

**Recommended:** Install Windows Terminal from Microsoft Store for a better experience

#### On Linux
1. Press `Ctrl + Alt + T`

**Alternative:** Search for "Terminal" in your applications

### Terminal Basics

#### The Prompt

When you open terminal, you'll see something like:
```
username@computer ~ $
```

This is called the **prompt**. It's waiting for you to type something.

- `username` - Your user name
- `computer` - Your computer's name
- `~` - Your current location (~ means home folder)
- `$` - Indicates you can type (sometimes `>` on Windows)

#### Your First Commands

Try these commands to get comfortable:

```bash
# See what folder you're in
pwd

# List files in current folder
ls

# On Windows, use:
dir

# Clear the screen
clear

# On Windows:
cls
```

#### Understanding Command Structure

Commands follow this pattern:
```
command [options] [arguments]
```

**Examples:**
```bash
# Command only
ls

# Command with option
ls -l

# Command with argument
cd Documents

# Command with option and argument
ls -l Documents
```

### Key Terminal Concepts

| Concept | Meaning | Example |
|---------|---------|---------|
| **Directory** | A folder | `Documents`, `Downloads` |
| **Path** | Location of a file/folder | `/Users/me/Documents/file.txt` |
| **Home Directory** | Your personal folder | `~` or `/Users/yourname` |
| **Current Directory** | Where you are now | Shown in prompt, check with `pwd` |
| **Root** | The top-level folder | `/` on Mac/Linux, `C:\` on Windows |

### Navigating Folders

```bash
# Go to a folder
cd Documents

# Go up one level
cd ..

# Go to home folder
cd ~

# Go to specific path
cd /Users/me/Projects

# Go back to previous folder
cd -
```

### Common Terminal Commands

| Command | What It Does | Example |
|---------|--------------|---------|
| `pwd` | Print current directory | `pwd` → `/Users/me` |
| `ls` | List files | `ls` → shows files |
| `cd` | Change directory | `cd Documents` |
| `mkdir` | Make directory | `mkdir new-folder` |
| `touch` | Create empty file | `touch file.txt` |
| `cat` | Show file contents | `cat file.txt` |
| `cp` | Copy file | `cp file.txt backup.txt` |
| `mv` | Move/rename file | `mv old.txt new.txt` |
| `rm` | Remove file | `rm file.txt` |
| `clear` | Clear screen | `clear` |

---

## Installation & First Run

### Prerequisites

Before installing Cortex Code, ensure you have:

1. **A Snowflake Account** - You need access to Snowflake
2. **Terminal Access** - Ability to run commands
3. **Internet Connection** - For AI communication

### Installing Cortex Code

#### Check If Already Installed

```bash
cortex --version
```

If you see a version number, you're good! If not, follow installation steps.

#### Installation Methods

**Via Homebrew (Mac):**
```bash
brew install snowflake-labs/tap/cortex
```

**Via pip (All platforms):**
```bash
pip install snowflake-cortex-cli
```

**Via conda:**
```bash
conda install -c snowflake cortex-cli
```

### Verifying Installation

```bash
# Check version
cortex --version

# Check help
cortex --help
```

### Setting Up Snowflake Connection

Cortex Code needs to connect to Snowflake. Create a connections file:

**Location:** `~/.snowflake/connections.toml`

```toml
# Default connection
[default]
account = "your-account.snowflakecomputing.com"
user = "your-username"
authenticator = "externalbrowser"

# Additional connections
[production]
account = "prod-account.snowflakecomputing.com"
user = "prod-user"
warehouse = "PROD_WH"
role = "ANALYST"
database = "PROD_DB"
schema = "PUBLIC"
```

**Authentication Options:**

| Method | Use Case | Config |
|--------|----------|--------|
| `externalbrowser` | SSO/Browser login | Opens browser to authenticate |
| `snowflake` | Username/password | Add `password = "xxx"` (not recommended) |
| `snowflake_jwt` | Key-pair auth | Add `private_key_path = "~/.snowflake/rsa_key.p8"` |
| `oauth` | OAuth token | Add `token = "xxx"` |

### First Run

```bash
# Start Cortex Code
cortex

# You should see:
# ╭─────────────────────────────────────╮
# │  Welcome to Cortex Code!            │
# │  Type your message and press Enter  │
# ╰─────────────────────────────────────╯
```

**What You'll See:**
- A welcome message
- A text input area
- Status indicators

**If Something Goes Wrong:**
- Run `/doctor` to diagnose issues
- Check your connection with `cortex connections list`
- Ensure Snowflake credentials are correct

---

## Your First Conversation

### Starting a Conversation

Once Cortex Code is running, just type and press Enter:

```
You: Hello! What can you help me with?

AI: I can help you with many things:
- Writing and explaining code
- Running terminal commands
- Querying Snowflake databases
- Creating files and projects
- Debugging issues
...
```

### Understanding the Interface

```
┌─────────────────────────────────────────────┐
│ Cortex Code                    [sonnet] [⚡] │  ← Header (model, mode)
├─────────────────────────────────────────────┤
│                                             │
│ You: Your message here                      │  ← Your messages
│                                             │
│ AI: Response from Cortex Code               │  ← AI responses
│     [Using tool: Read file.py]              │  ← Tool usage indicators
│                                             │
├─────────────────────────────────────────────┤
│ > Type your message...                      │  ← Input area
└─────────────────────────────────────────────┘
```

### Basic Interactions

#### Ask Questions
```
You: What is a Python list comprehension?

You: Explain how SQL JOINs work

You: What does this error mean? [paste error]
```

#### Request Actions
```
You: Create a Python file that reads CSV data

You: Run git status

You: Show me the contents of config.py
```

#### Get Help with Code
```
You: Fix this bug: [paste code]

You: Optimize this SQL query: [paste query]

You: Add error handling to this function: [paste code]
```

### Understanding Tool Usage

Cortex Code doesn't just talk - it can use tools:

| Tool | What It Does | You'll See |
|------|--------------|-----------|
| **Read** | Reads file contents | `[Reading src/app.py]` |
| **Write** | Creates new files | `[Writing new-file.py]` |
| **Edit** | Modifies existing files | `[Editing config.py]` |
| **Bash** | Runs terminal commands | `[Running: git status]` |
| **SQL Execute** | Runs Snowflake queries | `[Executing SQL]` |
| **Glob** | Finds files by pattern | `[Searching files]` |
| **Grep** | Searches file contents | `[Searching in files]` |

### Permission Prompts

By default, Cortex Code asks before making changes:

```
AI: I'll create a new file called app.py
    
    [Allow] [Deny] [Always Allow]
```

**Options:**
- **Allow** - Permit this one action
- **Deny** - Block this action
- **Always Allow** - Don't ask again for this type of action

### Exiting Cortex Code

```
# Type the quit command
/quit

# Or press Ctrl+C twice

# Or type
/q
```

---

## Basic Operations

### Asking Cortex Code to Read Files

**Simple request:**
```
You: Show me the contents of main.py
```

**Using @ syntax (faster):**
```
You: Explain this code @main.py
```

**Specific lines:**
```
You: What does line 50 do? @main.py$50

You: Explain lines 10-30 @main.py$10-30
```

### Asking Cortex Code to Write Files

```
You: Create a Python script that:
- Reads a CSV file
- Filters rows where sales > 1000
- Saves results to a new CSV
```

Cortex Code will:
1. Show you the code it will write
2. Ask permission
3. Create the file

### Asking Cortex Code to Run Commands

```
You: Run the tests

You: Install pandas using pip

You: Show me git log
```

**Quick bash with !:**
```
You: !ls -la

You: !git status

You: !python --version
```

### Asking Cortex Code to Query Snowflake

**Natural language:**
```
You: Show me the top 10 customers by revenue

You: How many orders were placed last month?

You: What tables are in the SALES database?
```

**Direct SQL:**
```
You: /sql SELECT * FROM customers LIMIT 10

You: /sql SHOW DATABASES
```

**With table context:**
```
You: #MYDB.SCHEMA.CUSTOMERS
Write a query to find customers who haven't ordered in 90 days
```

### Understanding Responses

Cortex Code responses include:

| Element | Meaning |
|---------|---------|
| Plain text | Explanations, answers |
| Code blocks | Code snippets (can be copied) |
| Tool indicators | Actions being taken |
| File paths | Links to files mentioned |
| Tables | Formatted data results |

---

# Part 2: Core Features (Intermediate)

---

## Slash Commands - Complete Reference

Slash commands start with `/` and control Cortex Code itself.

### Quick Reference Table

| Command | Category | Description |
|---------|----------|-------------|
| `/help` | Info | Show help |
| `/status` | Info | Session status |
| `/commands` | Info | List all commands |
| `/quit`, `/q` | Session | Exit |
| `/clear` | Session | Clear screen |
| `/new` | Session | New session |
| `/rename` | Session | Rename session |
| `/resume` | Session | Resume previous session |
| `/fork` | Session | Fork conversation |
| `/rewind` | Session | Undo messages |
| `/compact` | Session | Summarize & clear |
| `/plan` | Mode | Enable plan mode |
| `/plan-off` | Mode | Disable plan mode |
| `/bypass` | Mode | Enable bypass mode |
| `/bypass-off` | Mode | Disable bypass mode |
| `/model` | Mode | Switch AI model |
| `/settings` | Config | Settings editor |
| `/connections` | Config | Connection manager |
| `/skill` | Config | Skill manager |
| `/mcp` | Config | MCP manager |
| `/hooks` | Config | Hook manager |
| `/theme` | Config | Theme selector |
| `/agents` | Config | Agent manager |
| `/sh` | Dev | Run shell command |
| `/sql` | Dev | Execute SQL |
| `/table` | Dev | View SQL results |
| `/diff` | Dev | View git changes |
| `/add-dir` | Dev | Add working directory |
| `/worktree` | Dev | Worktree manager |
| `/sandbox` | Dev | Sandbox settings |
| `/tasks` | Utility | View active tasks |
| `/fdbt` | Utility | DBT operations |
| `/lineage` | Utility | Model lineage |
| `/setup-jupyter` | Utility | Setup Jupyter |
| `/feedback` | Utility | Submit feedback |
| `/clear-cache` | Utility | Clear caches |
| `/doctor` | Utility | Diagnose issues |
| `/update` | Utility | Update CLI |

### Detailed Command Documentation

---

#### `/help`

**Purpose:** Display help information and available commands.

**Usage:**
```
/help
```

**Output:** Shows keyboard shortcuts, special syntax, and command categories.

**When to use:** 
- You're stuck and don't know what to do
- You forgot a command
- You're new and exploring

---

#### `/status`

**Purpose:** Show current session information.

**Usage:**
```
/status
```

**Output:**
```
Session: my-project (abc123def)
Model: claude-sonnet-4-5
Mode: Confirm Actions
Connection: snowflake_production
Working Directory: /Users/me/projects/myapp
Context Usage: 45,230 / 200,000 tokens
```

**Information shown:**
- Session name and ID
- Current AI model
- Permission mode
- Active Snowflake connection
- Working directory
- Context window usage

**When to use:**
- Check which connection is active
- See how much context is used
- Verify your session settings

---

#### `/quit` / `/q`

**Purpose:** Exit Cortex Code.

**Usage:**
```
/quit
/q
```

**Behavior:**
- Saves session automatically
- Can be resumed later with `/resume`

**Alternatives:**
- Press `Ctrl+C` twice
- Close the terminal window

---

#### `/clear`

**Purpose:** Clear the terminal screen.

**Usage:**
```
/clear
```

**Note:** This only clears the visual display. Your conversation history remains intact.

---

#### `/new`

**Purpose:** Start a fresh conversation session.

**Usage:**
```
/new
```

**Behavior:**
- Previous session is saved and can be resumed
- Starts with empty context
- Maintains same connection and settings

**When to use:**
- Starting a completely different task
- Context is cluttered with irrelevant information
- Want a fresh start

---

#### `/rename`

**Purpose:** Give your session a memorable name.

**Usage:**
```
/rename my-feature-work
/rename bug-fix-auth-issue
/rename exploring-data-model
```

**Best practices:**
- Use descriptive names
- Include date for time-sensitive work: `2024-01-15-api-redesign`
- Use prefixes for categorization: `feature-`, `bugfix-`, `explore-`

**Why it matters:**
- Easier to find sessions later
- Better organization
- Quick identification when resuming

---

#### `/resume`

**Purpose:** Continue a previous conversation.

**Usage:**
```
/resume
```

**Interface:**
- Opens fullscreen session picker
- Shows last 100 sessions
- Displays session names, dates, and preview

**Navigation:**
- `Up/Down` arrows to scroll
- `Enter` to select
- `q` or `Escape` to cancel
- Type to search/filter

**Alternative (CLI):**
```bash
cortex --continue          # Resume last session
cortex -r last             # Same as above
cortex -r abc123           # Resume specific session
```

---

#### `/fork`

**Purpose:** Create a branch of your conversation to try alternatives.

**Usage:**
```
/fork                      # Fork with auto-generated name
/fork experiment-v2        # Fork with custom name
```

**How it works:**
1. Opens message selector
2. Choose which message to fork from
3. Creates new session branching from that point
4. Original session remains unchanged

**Use cases:**
- Try alternative approach without losing current work
- Experiment with risky changes
- Create checkpoint before major changes
- Explore multiple solutions in parallel

**Example workflow:**
```
You: Refactor the authentication system

AI: [Suggests approach A - using JWT]

You: /fork try-oauth
You: Actually, let's try OAuth instead

# Now you have two branches:
# - Original: JWT approach
# - Fork: OAuth approach
```

---

#### `/rewind`

**Purpose:** Roll back conversation to an earlier point.

**Usage:**
```
/rewind          # Interactive selector
/rewind 3        # Go back 3 user messages
```

**Behavior:**
- Messages after the rewind point are discarded
- Cannot be undone (use /fork first if unsure)

**When to use:**
- AI went down wrong path
- You made a mistake in your request
- Want to try different approach

**Difference from /fork:**
- `/fork` preserves original, creates branch
- `/rewind` discards messages permanently

---

#### `/compact`

**Purpose:** Summarize conversation and clear history to free up context space.

**Usage:**
```
/compact                              # Basic compaction
/compact focus on the API changes     # With focus instructions
/compact keep the database schema     # Preserve specific context
```

**How it works:**
1. AI summarizes key information from conversation
2. Clears message history
3. Summary becomes new context
4. Frees up token space

**When to use:**
- Context window getting full (check with `/status`)
- Conversation has lots of irrelevant history
- Switching focus within same session
- AI starting to "forget" earlier context

**Best practices:**
- Add focus instructions to preserve important context
- Compact before, not after, running out of space
- Use periodically in long sessions

---

#### `/plan`

**Purpose:** Enable plan mode for safer operations.

**Usage:**
```
/plan            # Enable
/plan-off        # Disable
```

**How plan mode works:**
1. You make a request
2. AI creates detailed plan
3. Shows you what it will do
4. You approve or reject
5. Only executes after approval

**Example:**
```
You: Refactor all the error handling

AI: Here's my plan:
    1. Read all Python files in src/
    2. Identify error handling patterns
    3. Create unified ErrorHandler class
    4. Update 15 files to use new pattern
    5. Update tests
    
    [Approve Plan] [Reject] [Modify]
```

**When to use:**
- Making significant changes
- Unfamiliar codebase
- Production-critical code
- Learning what AI will do

**Keyboard shortcut:** `Ctrl+P`

---

#### `/bypass`

**Purpose:** Auto-approve all tool calls (dangerous!).

**Usage:**
```
/bypass          # Enable
/bypass-off      # Disable
```

**Warning:** In bypass mode, the AI will:
- Create files without asking
- Modify files without asking
- Run commands without asking
- Execute SQL without asking

**When to use:**
- You completely trust the operations
- Doing repetitive tasks where prompts slow you down
- Automated/scripted workflows
- Quick prototyping in safe environment

**When NOT to use:**
- Production environments
- Unfamiliar codebases
- Irreversible operations
- Anything involving credentials/secrets

**Keyboard shortcut:** `Shift+Tab` cycles through modes

---

#### `/model`

**Purpose:** Switch the AI model being used.

**Usage:**
```
/model claude-sonnet-4-5    # Fast, balanced (default)
/model claude-opus-4-5      # Most capable
/model haiku                # Fastest, cheapest
```

**Model comparison:**

| Model | Speed | Capability | Cost | Best For |
|-------|-------|------------|------|----------|
| `haiku` | Fastest | Good | Lowest | Simple tasks, quick questions |
| `sonnet` | Fast | Great | Medium | Most development work |
| `opus` | Slower | Best | Highest | Complex architecture, difficult bugs |

**When to switch:**
- `haiku` for quick lookups, simple questions
- `sonnet` for regular development (default)
- `opus` for complex refactoring, architecture decisions

---

#### `/settings`

**Purpose:** Open interactive settings editor.

**Usage:**
```
/settings
```

**Interface:** Fullscreen editor showing all configurable options.

**Navigation:**
- Arrow keys to navigate
- Enter to edit value
- `q` to save and exit
- `Escape` to cancel

**Common settings:**
- Default model
- Theme
- Timeout values
- Feature flags

---

#### `/connections`

**Purpose:** Manage Snowflake database connections.

**Usage:**
```
/connections
```

**What you can do:**
- View all configured connections
- See active connection
- Switch between connections
- View connection details

**Alternative (CLI):**
```bash
cortex connections list
cortex connections set production
```

---

#### `/skill`

**Purpose:** Manage skills (reusable instruction sets).

**Usage:**
```
/skill
```

**Interface features:**
- List all skills by location (bundled, global, project)
- View skill details
- Add new skills
- Remove skills
- Check for conflicts

**Navigation:**
- `a` - Add skill
- `d` - Delete skill
- `Enter` - View details
- `q` - Exit

---

#### `/mcp`

**Purpose:** Manage MCP (Model Context Protocol) servers.

**Usage:**
```
/mcp
```

**What are MCP servers?**
External tools that extend Cortex Code's capabilities:
- GitHub integration
- Slack integration
- Custom APIs
- Database connections

**Interface features:**
- List configured servers
- Add/remove servers
- Check server status
- View server capabilities

---

#### `/hooks`

**Purpose:** Manage hooks (custom automation scripts).

**Usage:**
```
/hooks
```

**What are hooks?**
Scripts that run at specific points:
- Before/after tool execution
- On session start/end
- On permission requests

**Interface features:**
- View configured hooks
- Enable/disable hooks
- Add new hooks
- Test hooks

---

#### `/theme`

**Purpose:** Change color theme.

**Usage:**
```
/theme              # Open theme picker
/theme dark         # Set dark theme
/theme light        # Set light theme
```

**Available themes:**
- `dark` - Dark background, light text
- `light` - Light background, dark text
- `pro` - Professional theme

---

#### `/agents`

**Purpose:** View and manage AI subagents.

**Usage:**
```
/agents
```

**What you'll see:**
- Available agent types
- Currently running agents
- Agent outputs
- Agent management options
con
**Agent types:**
- `general-purpose` - Full capabilities
- `Explore` - Fast codebase exploration (read-only)
- `Plan` - Architecture planning (read-only)
- `feedback` - User feedback collection
- Custom agents you've created

---

#### `/sh`

**Purpose:** Execute a shell command.

**Usage:**
```
/sh git status
/sh npm install
/sh python script.py
```

**Difference from `!`:**
- `/sh` shows output to both you and AI
- `!` is shorthand, same behavior

**When to use:**
- Running build commands
- Checking system state
- Installing dependencies

---

#### `/sql`

**Purpose:** Execute SQL query on connected Snowflake database.

**Usage:**
```
/sql SELECT * FROM customers LIMIT 10
/sql SHOW DATABASES
/sql DESCRIBE TABLE my_table
```

**Multi-line queries:**
```
/sql 
SELECT 
    customer_id,
    SUM(amount) as total
FROM orders
GROUP BY customer_id
ORDER BY total DESC
LIMIT 10
```

**Viewing results:**
- Results shown inline
- Use `/table` for fullscreen view

---

#### `/table`

**Purpose:** View last SQL results in fullscreen table viewer.

**Usage:**
```
/table
```

**Navigation:**
- Arrow keys to scroll
- `q` to exit
- Page Up/Down for faster scrolling

---

#### `/diff`

**Purpose:** View uncommitted git changes.

**Usage:**
```
/diff                 # All changes
/diff --staged        # Only staged changes
```

**What you see:**
- Modified files
- Added/removed lines
- File statistics
- Commit-ready status

**Aliases:** `/changes`, `/review`

---

#### `/add-dir`

**Purpose:** Add additional working directory.

**Usage:**
```
/add-dir /path/to/other/project
/add-dir ../related-repo
```

**When to use:**
- Working across multiple repositories
- Need access to shared libraries
- Monorepo with multiple packages

---

#### `/worktree`

**Purpose:** Manage git worktrees for parallel development.

**Usage:**
```
/worktree
```

**What are worktrees?**
Separate working directories for the same repository, allowing you to:
- Work on multiple branches simultaneously
- Run multiple agents without conflicts
- Test changes in isolation

---

#### `/sandbox`

**Purpose:** Configure security sandbox settings.

**Usage:**
```
/sandbox
```

**What is the sandbox?**
Security boundary that restricts what commands can run. Configure:
- Allowed commands
- Blocked paths
- Network access

---

#### `/tasks`

**Purpose:** View currently running background tasks.

**Usage:**
```
/tasks
```

**Shows:**
- Active subagent tasks
- Background processes
- Task status and progress

---

#### `/fdbt`

**Purpose:** Quick access to dbt operations.

**Usage:**
```
/fdbt
```

**Available operations:**
- Run models
- Test models
- View lineage
- Check project status

---

#### `/lineage`

**Purpose:** View data lineage for a model.

**Usage:**
```
/lineage model_name
```

**Shows:**
- Upstream dependencies
- Downstream dependencies
- Data flow visualization

---

#### `/setup-jupyter`

**Purpose:** Configure Jupyter notebook integration.

**Usage:**
```
/setup-jupyter
```

**Enables:**
- Create notebooks
- Execute cells
- Manage kernels

---

#### `/feedback`

**Purpose:** Submit feedback to Cortex Code team.

**Usage:**
```
/feedback
```

**Types of feedback:**
- Bug reports
- Feature requests
- General feedback

---

#### `/clear-cache`

**Purpose:** Clear various caches.

**Usage:**
```
/clear-cache
```

**What gets cleared:**
- File caches
- Search caches
- Metadata caches

**When to use:**
- Stale data issues
- After major file changes
- Troubleshooting

---

#### `/doctor`

**Purpose:** Diagnose environment issues.

**Usage:**
```
/doctor
```

**Checks:**
- Snowflake connection
- File permissions
- Dependencies
- Configuration validity

**When to use:**
- Something isn't working
- After installation
- After configuration changes

---

#### `/update`

**Purpose:** Update Cortex Code to latest version.

**Usage:**
```
/update
```

**Options:**
- Update to latest stable
- Update to specific version
- Check for updates

---

## Special Syntax & Shortcuts

### Special Characters

Cortex Code uses special characters as shortcuts for common operations.

#### `@` - File References

Include file contents in your message.

| Syntax | Meaning | Example |
|--------|---------|---------|
| `@file` | Entire file | `@src/app.py` |
| `@file$N` | Single line | `@src/app.py$50` |
| `@file$N-M` | Line range | `@src/app.py$10-30` |
| `@file$N-` | Line to end | `@src/app.py$100-` |
| `@{file}` | Path with spaces | `@{my file.txt}` |

**Examples:**
```
# Explain entire file
Explain this code: @src/utils.py

# Focus on specific function (lines 25-45)
What does this function do? @src/utils.py$25-45

# Debug specific line
Why is line 100 causing an error? @src/main.py$100

# Compare two files
Compare these approaches:
@src/old_implementation.py
@src/new_implementation.py
```

**Pro tip:** Use tab completion after typing `@` to browse files.

---

#### `$` - Skills

Invoke a skill for specialized help.

**Syntax:** `$skill-name your request`

**Examples:**
```
$machine-learning help me train a classification model

$dbt-projects-on-snowflake run all models in staging

$streamlit create a dashboard for sales data

$cortex-code-guide how do I use worktrees?
```

**Available bundled skills:**
- `machine-learning` - ML workflows
- `dbt-projects-on-snowflake` - dbt operations
- `developing-with-streamlit` - Streamlit apps
- `cortex-code-guide` - This documentation
- `skill-development` - Create custom skills
- `openflow` - OpenFlow integration
- `data-governance` - Security analysis
- `cost-management` - Billing analysis

---

#### `#` - Snowflake Tables

Include table schema and sample data.

**Syntax:** `#DATABASE.SCHEMA.TABLE`

**Examples:**
```
# Single table
#SALES.PUBLIC.CUSTOMERS

# Multiple tables
#SALES.PUBLIC.CUSTOMERS #SALES.PUBLIC.ORDERS
Write a query to find customers with no orders

# With analysis request
#ANALYTICS.DW.FACT_SALES
What are the top selling products by region?
```

**What gets included:**
- Column names and types
- Sample rows (5-10)
- Primary/foreign keys if defined
- Table comments

**Pro tip:** Use this instead of describing tables manually - it's faster and more accurate.

---

#### `!` - Bash Commands

Execute shell commands quickly.

**Syntax:** `!command`

**Examples:**
```
!git status
!ls -la
!npm test
!python --version
!cat package.json
```

**Behavior:**
- Command runs immediately
- Output shown to you and AI
- AI can reference output in response

**Use cases:**
```
!git log --oneline -10
What was the last commit about?

!npm test 2>&1
Can you fix the failing tests?

!ls src/
Which file handles authentication?
```

---

#### `?` - Quick Help

Show help overlay instantly.

**Syntax:** `?`

**Shows:**
- Keyboard shortcuts
- Special syntax
- Quick tips

---

### Keyboard Shortcuts

#### Basic Input

| Shortcut | Action | When to Use |
|----------|--------|-------------|
| `Enter` | Send message | Submit your request |
| `Ctrl+J` | New line | Multi-line messages |
| `Ctrl+C` | Cancel/Exit | Stop operation, exit app |
| `Escape` | Stop generation | AI taking too long |
| `Ctrl+V` | Paste | Insert clipboard content |

#### Text Editing

| Shortcut | Action | Description |
|----------|--------|-------------|
| `Ctrl+U` | Clear line | Delete entire input |
| `Ctrl+K` | Kill to end | Delete from cursor to end |
| `Ctrl+W` | Delete word | Delete word before cursor |
| `Ctrl+Y` | Yank | Paste killed text |
| `Ctrl+A` | Start of line | Move cursor to beginning |
| `Ctrl+E` | End of line | Move cursor to end |

#### History Navigation

| Shortcut | Action | Description |
|----------|--------|-------------|
| `Up Arrow` | Previous | Previous command |
| `Down Arrow` | Next | Next command |
| `Ctrl+R` | Search | Search command history |

#### View Controls

| Shortcut | Action | Description |
|----------|--------|-------------|
| `Ctrl+O` | Cycle display | Compact → Expanded → Transcript |
| `Ctrl+P` | Plan mode | Toggle plan mode |
| `Ctrl+T` | Table view | Open SQL results table |
| `Ctrl+D` | Todo view | Open todo list |
| `Ctrl+B` | Background | Background current bash |
| `Ctrl+G` | Web search | View web search results |

#### Mode Switching

| Shortcut | Action | Description |
|----------|--------|-------------|
| `Shift+Tab` | Cycle modes | Confirm ↔ Bypass |

#### Fullscreen Views

When in fullscreen (table, diff, etc.):

| Shortcut | Action |
|----------|--------|
| `q` or `Escape` | Exit |
| `Up/Down` or `j/k` | Scroll line |
| `Page Up/Down` | Scroll page |
| `Home/End` | Top/Bottom |
| `g` | Go to line |
| `/` | Search |

---

## Working with Snowflake

### Connecting to Snowflake

#### View Connections
```bash
# CLI
cortex connections list

# In Cortex Code
/connections
```

#### Switch Connection
```bash
# CLI
cortex connections set production

# In Cortex Code (via /connections interface)
```

#### Start with Specific Connection
```bash
cortex -c production
cortex --connection my_snowflake_dev
```

### Running SQL Queries

#### Direct SQL
```
/sql SELECT * FROM MYDB.SCHEMA.TABLE LIMIT 10
```

#### Natural Language
```
You: Show me the top 10 customers by total purchases

AI: I'll query the database for that.
    [Executing SQL]
    
    Results:
    | CUSTOMER_ID | NAME | TOTAL_PURCHASES |
    |-------------|------|-----------------|
    | 1234        | ABC  | $1,234,567      |
    ...
```

#### With Table Context
```
You: #SALES.PUBLIC.ORDERS #SALES.PUBLIC.CUSTOMERS
     Find all customers who placed orders last month but not this month
```

### Viewing Results

```
# Inline results shown automatically

# Fullscreen table view
/table

# Export to file (ask AI)
You: Export these results to CSV
```

### Common Snowflake Operations

```
# Show databases
/sql SHOW DATABASES

# Show schemas
/sql SHOW SCHEMAS IN DATABASE mydb

# Show tables
/sql SHOW TABLES IN SCHEMA mydb.myschema

# Describe table
/sql DESCRIBE TABLE mydb.myschema.mytable

# Show warehouses
/sql SHOW WAREHOUSES

# Use specific warehouse
/sql USE WAREHOUSE my_warehouse
```

---

## Session Management

### Understanding Sessions

Every conversation with Cortex Code is a **session**. Sessions are:
- **Automatically saved** - Never lose your work
- **Resumable** - Continue where you left off
- **Forkable** - Create branches to try alternatives
- **Rewindable** - Go back in time

### Session Lifecycle

```
Start Session → Conversation → Save → Resume → Continue → End
                     ↓
                   Fork → New Branch
                     ↓
                  Rewind → Earlier State
                     ↓
                 Compact → Summarized State
```

### Best Practices

1. **Name important sessions** - Use `/rename` for sessions you'll return to
2. **Fork before experiments** - Use `/fork` before trying risky changes
3. **Compact periodically** - Use `/compact` in long sessions
4. **Use clear requests** - Each message should have a clear goal

### Session Organization Tips

**Naming conventions:**
```
/rename feature-user-authentication
/rename bugfix-login-timeout
/rename explore-new-api-design
/rename 2024-01-15-quarterly-analysis
```

**When to start new session:**
- Completely different project
- Unrelated task
- Clean slate needed

**When to continue session:**
- Same project
- Related tasks
- Building on previous work

---

## File Operations

### Reading Files

**Simple read:**
```
You: Show me the contents of app.py
```

**With @ syntax:**
```
You: @src/app.py
     Explain what this does
```

**Specific lines:**
```
You: @src/app.py$50-100
     What's happening in these lines?
```

### Creating Files

```
You: Create a Python script that:
     - Connects to an API
     - Fetches user data
     - Saves to JSON file
```

AI will:
1. Generate the code
2. Ask permission to create file
3. Create the file when approved

### Editing Files

```
You: In config.py, change the timeout from 30 to 60 seconds
```

AI will:
1. Read the file
2. Show the proposed change
3. Ask permission
4. Apply the edit when approved

### Searching Files

**Find by name:**
```
You: Find all Python files in src/
```

**Find by content:**
```
You: Find all files that import pandas
```

**Find and modify:**
```
You: Find all TODO comments and list them
```

---

# Part 3: Advanced Features (Expert)

---

## CLI Subcommands Deep Dive

### `cortex mcp` - MCP Server Management

MCP (Model Context Protocol) servers extend Cortex Code with external capabilities.

#### Understanding MCP

**What are MCP servers?**
- External processes that provide tools
- Can connect to APIs, databases, services
- Extend AI capabilities beyond built-in tools

**Architecture:**
```
Cortex Code ←→ MCP Server ←→ External Service
                    ↓
              Tool Definitions
              (list, create, update, etc.)
```

#### Managing MCP Servers

```bash
# List all configured servers
cortex mcp list

# Output:
# NAME      STATUS    TOOLS
# github    running   12 tools
# slack     stopped   8 tools
```

```bash
# Add new server
cortex mcp add github npx @modelcontextprotocol/server-github

# Add with environment variables
cortex mcp add myserver /path/to/server --env API_KEY=xxx

# Get server details
cortex mcp get github

# Remove server
cortex mcp remove github

# Start all servers
cortex mcp start
```

#### Creating Custom MCP Servers

MCP servers can be written in any language. Basic structure:

```javascript
// server.js
const { MCPServer } = require('@modelcontextprotocol/server');

const server = new MCPServer({
  name: 'my-custom-server',
  version: '1.0.0'
});

server.addTool({
  name: 'my_tool',
  description: 'Does something useful',
  parameters: {
    type: 'object',
    properties: {
      input: { type: 'string' }
    }
  },
  handler: async ({ input }) => {
    // Tool implementation
    return { result: `Processed: ${input}` };
  }
});

server.start();
```

#### MCP Configuration File

Location: `~/.snowflake/cortex/mcp.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "custom": {
      "command": "node",
      "args": ["/path/to/my-server.js"],
      "env": {
        "API_KEY": "${MY_API_KEY}"
      }
    }
  }
}
```

---

### `cortex skill` - Skill Management

#### Understanding Skills

**What are skills?**
- Reusable instruction sets
- Domain-specific knowledge
- Workflow templates
- Best practice guides

**How skills work:**
1. You invoke skill with `$skill-name`
2. Skill instructions load into context
3. AI follows skill guidance
4. Specialized behavior activated

#### Managing Skills

```bash
# List all skills
cortex skill list

# Output:
# LOCATION  NAME                    DESCRIPTION
# bundled   machine-learning        ML workflow assistance
# bundled   dbt-projects-on-snow... dbt on Snowflake
# global    my-custom-skill         My custom workflow
# project   team-conventions        Team coding standards

# Add skill from directory
cortex skill add /path/to/skill-directory

# Add skill from GitHub
cortex skill add https://github.com/org/skill-repo

# Add skill from tarball
cortex skill add https://example.com/skill.tar.gz

# Remove skill
cortex skill remove my-custom-skill
```

#### Skill Locations

Skills are loaded in priority order:

| Priority | Location | Use Case |
|----------|----------|----------|
| 1 | `.cortex/skills/` | Project-specific |
| 2 | `~/.snowflake/cortex/skills/` | Personal global |
| 3 | Remote (configured) | Shared team skills |
| 4 | Bundled | Built-in skills |

#### Skill Conflicts

When multiple skills have the same name, the highest priority wins. Check conflicts:

```
/skill
# Shows conflicts with [!] indicator
```

---

### `cortex worktree` - Git Worktree Management

#### Understanding Worktrees

**What are worktrees?**
Git feature allowing multiple working directories for one repository.

**Why use worktrees with Cortex Code?**
- Run multiple agents in parallel
- Work on different branches simultaneously
- Isolate experimental changes
- Avoid merge conflicts between agents

#### Managing Worktrees

```bash
# Create worktree for a branch
cortex worktree create feature-auth
# Creates: ../<repo>-worktrees/feature-auth/

# List all worktrees
cortex worktree list

# Output:
# BRANCH          PATH                                    STATUS
# main            /Users/me/project                       active
# feature-auth    /Users/me/project-worktrees/feature-auth ready
# feature-api     /Users/me/project-worktrees/feature-api  ready

# Switch to worktree
cortex worktree switch feature-auth

# Delete worktree
cortex worktree delete feature-auth

# Clean up agent-created worktrees
cortex worktree cleanup
```

#### Starting Cortex in Worktree

```bash
# Start in existing worktree
cortex --worktree feature-auth

# Create worktree and start in it
cortex --worktree new-feature
```

#### Parallel Agent Workflow

```bash
# Terminal 1: Main branch work
cortex

# Terminal 2: Feature branch (in worktree)
cortex --worktree feature-branch

# Both can run simultaneously without conflicts
```

---

### `cortex browser` - Browser Automation

#### Understanding Browser Automation

Cortex Code can control a web browser for:
- Web scraping
- UI testing
- Form automation
- Screenshot capture

#### Setup

```bash
# First-time installation
cortex browser install

# With system dependencies (Linux)
cortex browser install --with-deps
```

#### Core Commands

```bash
# Navigation
cortex browser open https://example.com
cortex browser back
cortex browser forward
cortex browser reload

# Get element references
cortex browser snapshot -i  # Interactive elements only

# Interact with elements (using refs from snapshot)
cortex browser click @e1
cortex browser fill @e2 "text to enter"
cortex browser select @e3 "option value"

# Screenshots
cortex browser screenshot output.png
cortex browser screenshot output.png --full  # Full page

# Close
cortex browser close
```

#### Element Selection

After running `snapshot -i`, you get element references:

```
@e1 [button] "Sign In"
@e2 [input] placeholder="Email"
@e3 [input] placeholder="Password"
@e4 [button] "Submit"
```

Use these refs in subsequent commands:
```bash
cortex browser fill @e2 "user@example.com"
cortex browser fill @e3 "password123"
cortex browser click @e4
```

#### Advanced Browser Operations

```bash
# Get element info
cortex browser get text @e1
cortex browser get html @e1
cortex browser get value @e2

# Check element state
cortex browser is visible @e1
cortex browser is enabled @e2

# Execute JavaScript
cortex browser eval "document.title"
cortex browser eval "window.scrollTo(0, 1000)"

# Set viewport
cortex browser set viewport 1920 1080
cortex browser set device "iPhone 12"

# Manage cookies
cortex browser cookies get
cortex browser cookies set name=value --domain=example.com
cortex browser cookies clear

# Network
cortex browser network requests
cortex browser network route "*/api/*" --abort  # Block API calls

# Sessions (isolated browser profiles)
cortex browser --session mysession open https://example.com
```

#### Automation Example

```bash
# Login automation script
cortex browser open https://app.example.com/login
cortex browser snapshot -i
cortex browser fill @e1 "username"
cortex browser fill @e2 "password"
cortex browser click @e3  # Login button
cortex browser wait 2000  # Wait 2 seconds
cortex browser screenshot logged-in.png
cortex browser close
```

---

### `cortex source` - Secure Credential Injection

#### Understanding Source

Injects Snowflake credentials into commands without exposing them.

**Why use this?**
- Never type passwords in terminal
- Credentials don't appear in logs
- Secure automated workflows

#### Usage

```bash
cortex source <connection> --map <field>=<VAR> -- <command>
```

**Available fields:**
- `account` - Snowflake account
- `user` - Username
- `password` - Password
- `authenticator` - Auth method
- `warehouse` - Warehouse name
- `database` - Database name
- `schema` - Schema name
- `role` - Role name
- `host` - Host URL
- `token` - Auth token

#### Examples

```bash
# Run Python script with credentials
cortex source myconn \
  --map account=SNOWFLAKE_ACCOUNT \
  --map user=SNOWFLAKE_USER \
  --map password=SNOWFLAKE_PASSWORD \
  -- python etl_script.py

# Run dbt
cortex source myconn \
  --map account=SNOWFLAKE_ACCOUNT \
  --map password=SNOWFLAKE_PASSWORD \
  -- dbt run

# Run shell script
cortex source myconn \
  --map account=SF_ACCOUNT \
  --map user=SF_USER \
  --map password=SF_PASS \
  -- ./deploy.sh
```

---

### `cortex ctx` - Context & Memory

#### Understanding Context

Long-term memory that persists across sessions.

**What can you store?**
- Facts - Things to remember
- Tasks - Work items
- Rules - Always-follow guidelines
- Files - Important file references

#### Commands

```bash
# Show all context
cortex ctx show all

# Remember a fact
cortex ctx remember "API rate limit is 1000 req/min"

# Add task
cortex ctx task add "Implement user authentication"

# List tasks
cortex ctx task list

# Mark task done
cortex ctx task done 1

# Add rule
cortex ctx rule add "Always write tests before committing"

# Add file reference
cortex ctx file add src/config.py important configuration

# List files
cortex ctx file list
```

#### Context in Sessions

Context automatically loads in new sessions:

```
You: What do I need to work on?

AI: Based on your context:
    
    Tasks:
    1. [ ] Implement user authentication
    2. [ ] Fix database timeout issue
    
    Remember:
    - API rate limit is 1000 req/min
    - Deploy requires VPN connection
```

---

### `cortex profile` - Profile Management

#### Understanding Profiles

Profiles are configuration bundles stored in Snowflake that can be shared across teams.

**What's in a profile?**
- Skills configurations
- MCP server settings
- Hooks
- Environment variables
- Custom settings

#### Commands

```bash
# List local profiles
cortex profile list

# List profiles in Snowflake
cortex profile list-remote -c my_connection

# Download profile
cortex profile add team-config -c my_connection

# Update profile from Snowflake
cortex profile sync team-config

# Show profile details
cortex profile show team-config

# Delete local profile
cortex profile delete team-config
```

#### Using Profiles

```bash
# Start with profile
cortex --profile team-data-engineering

# Profile applies:
# - Team-specific skills
# - Shared MCP servers
# - Common hooks
# - Standard settings
```

---

## Skills System

### Skill File Structure

```
my-skill/
├── SKILL.md              # Required: Main skill file
├── templates/            # Optional: File templates
│   ├── component.py
│   └── test.py
├── references/           # Optional: Reference docs
│   ├── api.md
│   └── examples.md
└── assets/               # Optional: Other assets
```

### SKILL.md Format

```markdown
---
name: my-skill
description: "What this skill does. Use when: situations. Triggers: keywords."
tools: ["bash", "edit", "read"]
---

# My Skill Name

## Overview
Brief description of what this skill helps with.

## Workflow
1. First step
2. Second step
3. Third step

## Commands
- `action1` - Description
- `action2` - Description

## Templates
Use templates in `templates/` directory.

## Stopping Points
- Before file changes: get user approval
- After major phases: confirm results

## Best Practices
- Practice 1
- Practice 2

## Examples

### Example 1: Basic Usage
```
$my-skill do something
```

### Example 2: Advanced Usage
```
$my-skill complex operation with options
```
```

### Frontmatter Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `name` | Yes | string | Unique identifier |
| `description` | Yes | string | Purpose + triggers |
| `tools` | No | array | Allowed tools (`["*"]` for all) |

### Creating Skills

#### Method 1: Manual Creation

```bash
# Create directory
mkdir -p ~/.snowflake/cortex/skills/my-skill

# Create SKILL.md
cat > ~/.snowflake/cortex/skills/my-skill/SKILL.md << 'EOF'
---
name: my-skill
description: "Helps with X. Use when: doing Y. Triggers: keyword1, keyword2."
---

# My Skill

Instructions for the AI...
EOF
```

#### Method 2: Interactive Creation

```
/skill
# Press 'a' to add
# Follow prompts
```

#### Method 3: Use skill-development Skill

```
$skill-development create a skill for API testing
```

### Advanced Skill Features

#### Tool Restrictions

Limit which tools the skill can use:

```yaml
---
name: read-only-analyzer
description: "Analyzes code without making changes"
tools: ["read", "glob", "grep"]
---
```

#### Templates

Include file templates in `templates/` directory:

```
my-skill/
├── SKILL.md
└── templates/
    ├── component.tsx
    └── test.spec.ts
```

Reference in SKILL.md:
```markdown
## Templates

When creating components, use the template at `templates/component.tsx`.
```

#### References

Include reference documentation:

```
my-skill/
├── SKILL.md
└── references/
    ├── api-docs.md
    └── examples.md
```

The AI can read these when needed.

### Skill Best Practices

1. **Keep skills focused** - One skill per domain
2. **Include triggers** - List keywords in description
3. **Define stopping points** - When to pause for approval
4. **Provide examples** - Show expected usage
5. **Keep under 500 lines** - Avoid context bloat
6. **Test incrementally** - Build and test in pieces

---

## Agents & Subagents

### Understanding Agents

Agents are autonomous AI workers that handle complex tasks independently.

**Main agent vs Subagents:**
- **Main agent** - Your conversation partner
- **Subagents** - Workers spawned for specific tasks

### Built-in Agent Types

| Type | Tools | Use Case |
|------|-------|----------|
| `general-purpose` | All | Complex multi-step tasks |
| `Explore` | Read-only | Fast codebase exploration |
| `Plan` | Read-only | Architecture planning |
| `feedback` | Limited | Collect user feedback |
| `dbt-verify` | Limited | dbt project verification |

### Spawning Subagents

Subagents are spawned automatically or on request:

```
You: Search the entire codebase for security vulnerabilities

AI: [Spawning Explore agent for comprehensive search]
```

```
You: Launch a Plan agent to design the authentication system

AI: [Spawning Plan agent]
```

### Background Agents

Run agents in background while you continue working:

```
You: In the background, analyze all API endpoints for documentation

AI: [Starting background agent: api-doc-analysis]
    Agent ID: abc123
```

Check status:
```
/agents
# Shows running agents, outputs, status
```

### Creating Custom Agents

#### Agent File Format

Location: `~/.snowflake/cortex/agents/` or `.cortex/agents/`

```markdown
---
name: security-scanner
description: "Scans code for security issues. Triggers: security, vulnerability, scan."
tools: ["read", "glob", "grep"]
---

# Security Scanner Agent

You are a security-focused code analyzer.

## Your Task
Scan the codebase for common security vulnerabilities:
- SQL injection
- XSS
- CSRF
- Hardcoded secrets
- Insecure dependencies

## Process
1. Search for patterns indicating vulnerabilities
2. Analyze suspicious code
3. Generate report with findings
4. Suggest fixes

## Output Format
For each finding:
- Location (file:line)
- Severity (High/Medium/Low)
- Description
- Suggested fix
```

#### Agent Frontmatter

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier |
| `description` | Yes | Purpose + triggers |
| `tools` | No | Allowed tools |

### Worktree Isolation

For parallel agents, use worktree isolation:

```
You: Launch background agents to work on auth and API simultaneously

AI: [Creating worktree for auth-agent]
    [Creating worktree for api-agent]
    [Starting both agents in isolation]
```

This prevents file conflicts between agents.

### Agent Best Practices

1. **Use Explore for search** - Faster and cheaper than general-purpose
2. **Use Plan for design** - Won't accidentally modify files
3. **Isolate parallel agents** - Use worktrees to prevent conflicts
4. **Define clear tasks** - Specific prompts get better results
5. **Monitor background agents** - Check `/agents` periodically

---

## Hooks System

### Understanding Hooks

Hooks are custom scripts that run at specific points during Cortex Code execution.

**Use cases:**
- Validate operations before execution
- Log actions for auditing
- Transform inputs/outputs
- Enforce policies
- Integrate with external systems

### Hook Events

| Event | When | Common Uses |
|-------|------|-------------|
| `PreToolUse` | Before tool runs | Validate, block dangerous ops |
| `PostToolUse` | After tool runs | Log, validate output |
| `PermissionRequest` | Permission prompt | Custom approval logic |
| `UserPromptSubmit` | User sends message | Transform, validate input |
| `Stop` | Agent stops | Verify completion |
| `SubagentStop` | Subagent stops | Handle subagent results |
| `Notification` | On notification | Custom notification handling |
| `SessionStart` | Session begins | Initialize context |
| `SessionEnd` | Session ends | Cleanup, logging |
| `PreCompact` | Before compaction | Inject context |
| `Setup` | Initial setup | One-time actions |

### Hook Configuration

Location: `~/.snowflake/cortex/hooks.json`

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/validate-bash.sh",
            "timeout": 30,
            "enabled": true
          }
        ]
      }
    ],
    "SessionStart": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/init.sh"
          }
        ]
      }
    ]
  }
}
```

### Matchers

Pattern matching for tool-specific hooks:

| Pattern | Matches |
|---------|---------|
| `Bash` | Exactly "Bash" |
| `Bash\|Edit` | "Bash" or "Edit" |
| `.*` or `*` | All tools |
| `snowflake_.*` | All Snowflake tools |
| `mcp__github__.*` | All GitHub MCP tools |

### Writing Hook Scripts

#### Input (stdin)

Hooks receive JSON on stdin:

```json
{
  "session_id": "abc123",
  "hook_event_name": "PreToolUse",
  "tool_name": "Bash",
  "tool_input": {
    "command": "rm -rf /important"
  },
  "cwd": "/Users/me/project",
  "permission_mode": "default"
}
```

#### Output

**Exit codes:**
| Code | Meaning |
|------|---------|
| 0 | Success, continue |
| 2 | Block operation |
| Other | Non-blocking error |

**JSON output (optional):**

```json
{
  "decision": "block",
  "reason": "Dangerous command detected",
  "continue": false
}
```

#### Example Hook: Block Dangerous Commands

```bash
#!/bin/bash
# validate-bash.sh

# Read input
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

# Check for dangerous patterns
if [[ "$COMMAND" == *"rm -rf"* ]]; then
  echo '{"decision": "block", "reason": "rm -rf is not allowed"}' >&2
  exit 2
fi

if [[ "$COMMAND" == *"DROP DATABASE"* ]]; then
  echo '{"decision": "block", "reason": "DROP DATABASE is not allowed"}' >&2
  exit 2
fi

# Allow command
exit 0
```

Make executable:
```bash
chmod +x /path/to/validate-bash.sh
```

#### Example Hook: Log All Operations

```bash
#!/bin/bash
# log-operations.sh

INPUT=$(cat)
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
TOOL=$(echo "$INPUT" | jq -r '.tool_name // "unknown"')

# Log to file
echo "$TIMESTAMP | $TOOL | $INPUT" >> ~/cortex-audit.log

# Always allow
exit 0
```

### Managing Hooks

```
/hooks
```

Interface features:
- View all configured hooks
- Enable/disable hooks
- Test hook execution
- View hook logs

### Hook Best Practices

1. **Keep hooks fast** - Timeout applies
2. **Exit 0 for non-critical** - Only exit 2 for true blocks
3. **Log to files** - Don't pollute stdout
4. **Use absolute paths** - Avoid path issues
5. **Test manually first** - Debug before configuring

---

## MCP Integration

### What is MCP?

Model Context Protocol (MCP) is a standard for AI tool integration:
- **Servers** provide tools
- **Clients** (Cortex Code) use tools
- **Protocol** defines communication

### MCP Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Cortex Code │────▶│ MCP Server  │────▶│  External   │
│  (Client)   │◀────│  (Tools)    │◀────│   Service   │
└─────────────┘     └─────────────┘     └─────────────┘
```

### Popular MCP Servers

| Server | Purpose | Tools |
|--------|---------|-------|
| `server-github` | GitHub integration | repos, issues, PRs |
| `server-slack` | Slack integration | messages, channels |
| `server-postgres` | PostgreSQL | queries, schema |
| `server-filesystem` | File operations | read, write, list |
| `server-fetch` | HTTP requests | GET, POST, etc. |

### Installing MCP Servers

```bash
# GitHub
cortex mcp add github npx @modelcontextprotocol/server-github

# Slack
cortex mcp add slack npx @modelcontextprotocol/server-slack

# Custom server
cortex mcp add custom node /path/to/server.js
```

### MCP Configuration

Location: `~/.snowflake/cortex/mcp.json`

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@host:5432/db"
      }
    }
  }
}
```

### Environment Variables

Use `${VAR}` syntax to reference environment variables:

```json
{
  "env": {
    "API_KEY": "${MY_API_KEY}",
    "SECRET": "${MY_SECRET}"
  }
}
```

Set in your shell:
```bash
export MY_API_KEY="xxx"
export MY_SECRET="yyy"
```

### Creating Custom MCP Servers

Basic structure (Node.js):

```javascript
const { Server } = require('@modelcontextprotocol/sdk/server');
const { StdioServerTransport } = require('@modelcontextprotocol/sdk/server/stdio');

const server = new Server({
  name: 'my-server',
  version: '1.0.0'
}, {
  capabilities: {
    tools: {}
  }
});

// Define tools
server.setRequestHandler('tools/list', async () => ({
  tools: [{
    name: 'my_tool',
    description: 'Does something useful',
    inputSchema: {
      type: 'object',
      properties: {
        input: { type: 'string' }
      },
      required: ['input']
    }
  }]
}));

server.setRequestHandler('tools/call', async (request) => {
  if (request.params.name === 'my_tool') {
    const input = request.params.arguments.input;
    return {
      content: [{
        type: 'text',
        text: `Processed: ${input}`
      }]
    };
  }
});

// Start server
const transport = new StdioServerTransport();
server.connect(transport);
```

---

## Browser Automation

### Complete Command Reference

#### Navigation

```bash
cortex browser open <url>           # Open URL
cortex browser back                 # Go back
cortex browser forward              # Go forward
cortex browser reload               # Reload page
```

#### Element Interaction

```bash
cortex browser click <selector>     # Click element
cortex browser dblclick <selector>  # Double-click
cortex browser type <sel> <text>    # Type text
cortex browser fill <sel> <text>    # Clear and fill
cortex browser press <key>          # Press key
cortex browser hover <selector>     # Hover element
cortex browser focus <selector>     # Focus element
cortex browser check <selector>     # Check checkbox
cortex browser uncheck <selector>   # Uncheck checkbox
cortex browser select <sel> <val>   # Select dropdown option
cortex browser drag <src> <dst>     # Drag and drop
cortex browser upload <sel> <file>  # Upload file
```

#### Information Retrieval

```bash
cortex browser snapshot             # Full accessibility tree
cortex browser snapshot -i          # Interactive elements only
cortex browser snapshot -c          # Compact output
cortex browser get text <sel>       # Get text content
cortex browser get html <sel>       # Get HTML
cortex browser get value <sel>      # Get input value
cortex browser get attr <sel> <name> # Get attribute
cortex browser get title            # Get page title
cortex browser get url              # Get current URL
```

#### State Checking

```bash
cortex browser is visible <sel>     # Check visibility
cortex browser is enabled <sel>     # Check if enabled
cortex browser is checked <sel>     # Check if checked
```

#### Screenshots & Recording

```bash
cortex browser screenshot [path]    # Take screenshot
cortex browser screenshot -f [path] # Full page
cortex browser pdf <path>           # Save as PDF
cortex browser record start <path>  # Start video
cortex browser record stop          # Stop video
```

#### Settings

```bash
cortex browser set viewport 1920 1080       # Set viewport size
cortex browser set device "iPhone 12"       # Emulate device
cortex browser set geo 40.7128 -74.0060     # Set geolocation
cortex browser set offline                  # Go offline
cortex browser set headers '{"Auth":"xxx"}' # Set headers
```

#### Sessions & Profiles

```bash
cortex browser --session name ...   # Isolated session
cortex browser --profile path ...   # Persistent profile
cortex browser session list         # List sessions
```

#### Debug

```bash
cortex browser console              # View console logs
cortex browser errors               # View page errors
cortex browser highlight <sel>      # Highlight element
cortex browser trace start          # Start trace
cortex browser trace stop           # Stop trace
```

### Workflow Example: E2E Test

```bash
#!/bin/bash
# e2e-test.sh

# Setup
cortex browser install

# Login flow
cortex browser open https://myapp.com/login
cortex browser snapshot -i

# Fill login form
cortex browser fill @e1 "test@example.com"
cortex browser fill @e2 "password123"
cortex browser click @e3

# Wait for redirect
cortex browser wait 2000

# Verify logged in
cortex browser screenshot logged-in.png
cortex browser get text ".user-name"

# Cleanup
cortex browser close
```

---

## Profiles

### Understanding Profiles

Profiles are configuration bundles stored in Snowflake:
- Share configurations across team
- Standardize setups
- Version control configurations

### Profile Contents

A profile can include:
- Skills
- MCP server configurations
- Hook configurations
- Environment variables
- Custom settings

### Profile Commands

```bash
# List local profiles
cortex profile list

# List remote profiles (in Snowflake)
cortex profile list-remote -c connection_name

# Download profile
cortex profile add profile_name -c connection_name

# Update profile from remote
cortex profile sync profile_name

# View profile details
cortex profile show profile_name

# Delete local profile
cortex profile delete profile_name
```

### Using Profiles

```bash
# Start with profile
cortex --profile team-data-eng

# Profile overrides default settings
```

### Creating Profiles

Profiles are created and managed in Snowflake (admin function):

```sql
-- Example: Create profile registry
CREATE TABLE cortex_profiles (
    name VARCHAR,
    config VARIANT,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);

-- Insert profile
INSERT INTO cortex_profiles (name, config, created_at, updated_at)
VALUES (
    'team-data-eng',
    PARSE_JSON('{
        "skills": ["dbt-projects-on-snowflake", "machine-learning"],
        "mcp": {},
        "settings": {}
    }'),
    CURRENT_TIMESTAMP(),
    CURRENT_TIMESTAMP()
);
```

---

# Part 4: Mastery

---

## Configuration Deep Dive

### Configuration Hierarchy

Settings are applied in this order (later overrides earlier):

```
Defaults → Settings File → Environment Variables → CLI Flags
```

### Complete Settings Reference

Location: `~/.snowflake/cortex/settings.json`

```json
{
  "env": {
    "CORTEX_AGENT_MODEL": "claude-sonnet-4-5",
    "CORTEX_AGENT_ENABLE_SUBAGENTS": true,
    "CORTEX_CODE_STREAMING": false,
    "CORTEX_ENABLE_MEMORY": false,
    "CORTEX_ENABLE_EXPERIMENTAL_SKILLS": false,
    "SNOVA_DEBUG": false,
    "SNOVA_NO_HISTORY_MODE": false
  },
  "theme": "dark",
  "diffDisplayMode": "unified",
  "compactMode": false,
  "bashDefaultTimeoutMs": 180000
}
```

### Environment Variables

| Variable | Type | Description |
|----------|------|-------------|
| `CORTEX_AGENT_MODEL` | string | Default model |
| `CORTEX_AGENT_ENABLE_SUBAGENTS` | boolean | Allow subagent spawning |
| `CORTEX_CODE_STREAMING` | boolean | Enable streaming output |
| `CORTEX_ENABLE_MEMORY` | boolean | Enable memory tool |
| `CORTEX_ENABLE_EXPERIMENTAL_SKILLS` | boolean | Enable experimental features |
| `SNOVA_DEBUG` | boolean | Debug logging |
| `SNOVA_NO_HISTORY_MODE` | boolean | Don't save history |
| `SNOWFLAKE_CONNECTION` | string | Default connection |

### Connection Configuration

Location: `~/.snowflake/connections.toml`

```toml
[default]
account = "account.snowflakecomputing.com"
user = "username"
authenticator = "externalbrowser"

[production]
account = "prod.snowflakecomputing.com"
user = "prod_user"
authenticator = "snowflake_jwt"
private_key_path = "~/.snowflake/prod_key.p8"
warehouse = "PROD_WH"
database = "PROD_DB"
schema = "PUBLIC"
role = "ANALYST"

[development]
account = "dev.snowflakecomputing.com"
user = "dev_user"
password = "${SNOWFLAKE_DEV_PASSWORD}"
warehouse = "DEV_WH"
database = "DEV_DB"
schema = "SANDBOX"
role = "DEVELOPER"
```

### Authentication Methods

| Method | Config | Use Case |
|--------|--------|----------|
| Browser SSO | `authenticator = "externalbrowser"` | Interactive login |
| Key-pair | `authenticator = "snowflake_jwt"` + `private_key_path` | Automation |
| Password | `password = "xxx"` | Development only |
| OAuth | `authenticator = "oauth"` + `token` | Enterprise SSO |
| PAT | `authenticator = "PROGRAMMATIC_ACCESS_TOKEN"` | Service accounts |

### Directory Structure

```
~/.snowflake/
├── connections.toml          # Snowflake connections
└── cortex/
    ├── settings.json         # Main settings
    ├── skills.json           # Skills config
    ├── hooks.json            # Hooks config
    ├── mcp.json              # MCP config
    ├── permissions.json      # Permission history (auto)
    ├── skills/               # Global skills
    ├── agents/               # Custom agents
    ├── commands/             # Custom commands
    ├── profiles/             # Downloaded profiles
    └── conversations/        # Session history
```

---

## Custom Development

### Creating Custom Slash Commands

Location: `~/.snowflake/cortex/commands/` or `.cortex/commands/`

**Example: `/deploy` command**

```markdown
<!-- ~/.snowflake/cortex/commands/deploy.md -->
---
description: Deploy to production
---

# Deploy Command

You are helping the user deploy to production.

## Steps
1. Run tests
2. Build application
3. Deploy to staging
4. Run smoke tests
5. Deploy to production

## Checklist
- [ ] All tests passing
- [ ] Version updated
- [ ] Changelog updated
- [ ] Approval obtained
```

Usage:
```
/deploy
```

### Creating Custom Skills

See [Skills System](#skills-system) for detailed instructions.

### Creating Custom Agents

See [Agents & Subagents](#agents--subagents) for detailed instructions.

### Creating Custom Hooks

See [Hooks System](#hooks-system) for detailed instructions.

---

## Performance Optimization

### Context Management

**Problem:** Context window fills up, AI forgets things.

**Solutions:**
1. **Use `/compact` regularly** - Summarize and free space
2. **Be specific** - Don't include unnecessary files
3. **Use line ranges** - `@file$10-50` instead of `@file`
4. **Check usage** - `/status` shows context usage

### Response Speed

**Faster responses:**
1. Use `haiku` model for simple tasks
2. Use `Explore` agent for searches
3. Be specific in requests
4. Avoid overly broad questions

### Subagent Efficiency

**Best practices:**
1. Use `Explore` for read-only operations (faster than general-purpose)
2. Use worktree isolation for parallel agents
3. Be specific about agent tasks
4. Monitor background agents with `/agents`

---

## Security Best Practices

### Credential Management

**DO:**
- Use `externalbrowser` or `snowflake_jwt` auth
- Store keys in secure locations
- Use `cortex source` for credential injection
- Use environment variables for secrets

**DON'T:**
- Store passwords in connections.toml
- Commit credentials to git
- Share connection files
- Log credentials

### Hook Security

**DO:**
- Validate all tool inputs
- Block dangerous commands
- Log for auditing
- Use exit code 2 for blocks

**DON'T:**
- Trust user input blindly
- Allow `rm -rf` without validation
- Skip validation in bypass mode

### Sandbox Configuration

Configure sandbox to restrict dangerous operations:

```
/sandbox
```

Options:
- Allowed commands
- Blocked paths
- Network restrictions

---

## Troubleshooting

### Common Issues

#### "Connection failed"

```bash
# Check connection
cortex connections list

# Test connection
cortex -c my_connection -p "SELECT 1"

# Check credentials
cat ~/.snowflake/connections.toml
```

#### "Context too long"

```
/compact
# Or start new session
/new
```

#### "Tool not working"

```
/doctor
# Checks environment and dependencies
```

#### "MCP server not starting"

```bash
# Check server status
cortex mcp list

# Try starting manually
npx @modelcontextprotocol/server-github

# Check logs
SNOVA_DEBUG=true cortex
```

#### "Permission denied"

```bash
# Check file permissions
ls -la ~/.snowflake/cortex/

# Fix permissions
chmod 700 ~/.snowflake/cortex/
chmod 600 ~/.snowflake/connections.toml
```

### Debug Mode

```bash
# Enable debug logging
SNOVA_DEBUG=true cortex

# Verbose output shows:
# - Tool calls
# - API requests
# - Error details
```

### Getting Help

1. `/doctor` - Diagnose issues
2. `/help` - Show help
3. `/feedback` - Report issues
4. `cortex --help` - CLI help

---

## Real-World Workflows

### Workflow 1: Data Analysis

```bash
# Start with Snowflake connection
cortex -c production

# In Cortex Code:
You: #ANALYTICS.DW.FACT_SALES
     Analyze sales trends for Q4 2024

You: Create a visualization of monthly revenue

You: Export results to CSV and create a summary report
```

### Workflow 2: Code Review

```bash
cortex --plan

You: Review all changes in this PR for:
     - Security issues
     - Performance problems
     - Code style violations

You: !git diff origin/main...HEAD

You: Generate a code review report
```

### Workflow 3: dbt Development

```bash
cortex -c dev_connection

You: $dbt-projects-on-snowflake

You: Create a new model for customer lifetime value

You: Run the model and validate results

You: Add tests for the new model
```

### Workflow 4: Automated Testing

```bash
cortex --bypass

You: Run all tests and fix any failures

You: Add missing test coverage for the auth module

You: Generate a coverage report
```

### Workflow 5: Documentation

```bash
cortex

You: Generate API documentation for all endpoints in src/api/

You: Create a README for the project

You: Add inline comments to complex functions
```

### Workflow 6: Database Migration

```bash
cortex -c staging --plan

You: Design a migration to add user preferences table

# Review plan, approve

You: Apply migration to staging

You: Verify migration succeeded

You: Prepare production migration script
```

---

## Quick Reference

### Essential Commands

| Command | Purpose |
|---------|---------|
| `cortex` | Start Cortex Code |
| `cortex --continue` | Resume last session |
| `cortex -c <conn>` | Use specific connection |
| `/help` | Show help |
| `/status` | Session info |
| `/quit` | Exit |
| `/compact` | Free context space |
| `/plan` | Safe mode |

### Essential Syntax

| Syntax | Purpose | Example |
|--------|---------|---------|
| `@file` | Include file | `@src/app.py` |
| `@file$N-M` | Include lines | `@src/app.py$10-50` |
| `$skill` | Use skill | `$ml help` |
| `#table` | Include table | `#DB.SCHEMA.TABLE` |
| `!cmd` | Run command | `!git status` |
| `/cmd` | Slash command | `/help` |

### Essential Shortcuts

| Shortcut | Action |
|----------|--------|
| `Enter` | Send message |
| `Ctrl+J` | New line |
| `Ctrl+C` | Cancel/Exit |
| `Escape` | Stop generation |
| `Shift+Tab` | Cycle modes |
| `Ctrl+P` | Toggle plan mode |
| `Up/Down` | Command history |
| `Ctrl+R` | Search history |

### Model Selection

| Model | Speed | Use For |
|-------|-------|---------|
| `haiku` | Fastest | Simple questions |
| `sonnet` | Fast | Most work (default) |
| `opus` | Slower | Complex tasks |

---

## Appendix: Complete Command List

### CLI Commands

```bash
cortex                              # Start REPL
cortex -p "message"                 # Non-interactive
cortex -r last                      # Resume last
cortex -c connection                # Use connection
cortex --plan                       # Plan mode
cortex --bypass                     # Bypass mode
cortex --profile name               # Use profile
cortex --worktree name              # Use worktree

cortex connections list             # List connections
cortex connections set name         # Set connection

cortex mcp list                     # List MCP servers
cortex mcp add name command         # Add server
cortex mcp remove name              # Remove server
cortex mcp get name                 # Get server info
cortex mcp start                    # Start servers

cortex skill list                   # List skills
cortex skill add path               # Add skill
cortex skill remove name            # Remove skill

cortex profile list                 # List profiles
cortex profile list-remote -c conn  # List remote
cortex profile add name -c conn     # Add profile
cortex profile sync name            # Sync profile
cortex profile show name            # Show profile
cortex profile delete name          # Delete profile

cortex worktree create name         # Create worktree
cortex worktree list                # List worktrees
cortex worktree switch name         # Switch worktree
cortex worktree delete name         # Delete worktree
cortex worktree cleanup             # Clean worktrees

cortex browser install              # Install browser
cortex browser open url             # Open URL
cortex browser snapshot -i          # Get elements
cortex browser click @ref           # Click element
cortex browser fill @ref text       # Fill field
cortex browser close                # Close browser

cortex source conn --map f=V -- cmd # Run with creds

cortex ctx show all                 # Show context
cortex ctx remember "fact"          # Remember
cortex ctx task add "task"          # Add task
cortex ctx task list                # List tasks
cortex ctx rule add "rule"          # Add rule

cortex resume                       # Resume picker
cortex update                       # Update CLI
cortex versions                     # List versions
cortex completion install           # Install completion
```

### Slash Commands

```
# Session
/help, /status, /commands
/quit, /q, /clear, /new
/rename, /resume, /fork, /rewind, /compact

# Mode
/plan, /plan-off, /bypass, /bypass-off, /model

# Config
/settings, /connections, /skill, /mcp, /hooks, /theme, /agents

# Dev
/sh, /sql, /table, /diff, /add-dir, /worktree, /sandbox

# Utility
/tasks, /fdbt, /lineage, /setup-jupyter
/feedback, /clear-cache, /doctor, /update
```

---

*This guide covers Cortex Code CLI version 1.0.10+. Features may vary in different versions.*

*Last updated: Jan 2026*
