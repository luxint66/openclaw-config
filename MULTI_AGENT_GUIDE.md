# 🦞 Multi-Agent System - User Guide

## 🎯 Overview

You now have a system to create and manage specialized AI agents that persist and can be called on-demand.

**Key Benefits:**
- **Persistent** - Agents stay alive across sessions
- **Specialized** - Each agent has a specific domain expertise
- **On-Demand** - Call agents when you need their expertise
- **Independent** - Agents run in isolated sessions with their own memory

---

## 🛠️ Quick Start

### 1. Create Your First Agent

```bash
cd /home/luxinterior/.openclaw/workspace
./create_agents.sh create hedge-specialist
```

This will create a background agent that specializes in Polymarket hedging.

### 2. List All Active Agents

```bash
./create_agents.sh list
```

This shows all running sub-agent sessions.

### 3. Send Tasks to an Agent

```bash
# Send a task to the hedge specialist
./create_agents.sh spawn hedge-specialist
# OR directly:
polyclaw sessions_send --label hedge-specialist "Find hedges in trending markets with 85%+ coverage"
```

### 4. Monitor an Agent's Activity

```bash
./create_agents.sh monitor hedge-specialist
```

---

## 📋 Available Agent Types

| Agent Type | Specialization | Use When... |
|-------------|----------------|--------------|
| **hedge-specialist** | Polymarket hedge discovery, analysis, position sizing | Trading, market analysis, finding hedges |
| **code-reviewer** | Code quality, best practices, bugs, security | Reviewing PRs, refactoring code, security audits |
| **researcher** | Web research, data gathering, synthesis | Investigating topics, competitive analysis, fact-checking |
| **data-analyst** | Data analysis, visualization, insights | Processing datasets, generating reports, statistics |
| **tester** | Test execution, validation, regression testing | Testing systems, edge cases, performance testing |

---

## 💬 How to Communicate with Agents

### Option 1: Direct Message

```bash
# Send a message directly
polyclaw sessions_send --label hedge-specialist "Analyze these 5 markets for hedging opportunities"
```

### Option 2: Via Create Script

```bash
# Spawn with a task
./create_agents.sh spawn hedge-specialist

# This creates the agent and gives it its first task
```

### Option 3: Interactive Sessions

You can start an interactive session with any agent:

```bash
# This depends on how your OpenClaw is configured
# The agent will be accessible via its label
```

---

## 🔄 Agent Lifecycle

### Creation
- Agent created with `--cleanup keep` - **stays alive**
- Runs in isolated session
- Has its own memory and context
- Announces itself back to main session when done

### Task Assignment
- Send clear, specific tasks
- Provide relevant context (files, data, etc.)
- Set reasonable timeouts

### Result Handling
- Agents report results back to main session
- They can create files, run commands, etc.
- Results are visible to main session

### Monitoring
- Use `sessions_list` to see all active agents
- Use `sessions_history` to see past interactions
- Agents remain persistent until explicitly stopped

---

## 🎯 Example Workflows

### Workflow 1: Hedge Research

```bash
# 1. Create hedge specialist (if not already exists)
./create_agents.sh create hedge-specialist

# 2. Scan for hedges
polyclaw sessions_send --label hedge-specialist "Scan top 20 trending markets for hedges with 90%+ coverage, Tier 1-2 only, sort by volume. Save results to workspace."

# 3. Monitor results
./create_agents.sh monitor hedge-specialist

# 4. Check dashboard
# Dashboard at http://107.174.92.36:8501 will show new hedges
```

### Workflow 2: Code Review

```bash
# 1. Create code reviewer (if not already exists)
./create_agents.sh create code-reviewer

# 2. Send code to review
polyclaw sessions_send --label code-reviewer "Review /home/luxinterior/.openclaw/skills/polyclaw/testing/cost_calculator.py for: 1) Code quality 2) Error handling 3) Performance 4) Security issues"

# 3. Get detailed feedback
# Agent will analyze and provide specific suggestions
```

### Workflow 3: Research

```bash
# 1. Create researcher (if not already exists)
./create_agents.sh create researcher

# 2. Assign research task
polyclaw sessions_send --label researcher "Research Polymarket CLOB API order book depth. Find: 1) How to fetch 2) What data format 3) Any rate limits. Document with examples."

# 3. Get research summary
# Agent will search, synthesize, and report back
```

---

## 🧠 Advanced Usage

### Parallel Execution

Run multiple agents in parallel for different tasks:

```bash
# Terminal 1: Scan markets
./create_agents.sh spawn hedge-specialist

# Terminal 2: Research competitors
./create_agents.sh create researcher
./create_agents.sh spawn researcher "Research other prediction market platforms"

# Terminal 3: Analyze data
./create_agents.sh create data-analyst
./create_agents.sh spawn data-analyst "Analyze yesterday's hedge scan results"
```

### Agent Collaboration

Agents can communicate with each other:

```bash
# Main agent coordinates sub-agents
polyclaw sessions_send --label hedge-specialist "Use the code-reviewer agent to verify the hedge_scanner.py file for security issues before next scan"
```

### Persistent Background Agents

Create long-running monitoring agents:

```bash
# Create a 24/7 market monitor
./create_agents.sh create hedge-specialist

# Set up as recurring monitoring
# Agent monitors markets and reports opportunities automatically
```

---

## 🔧 Management Commands Reference

| Command | Description | Example |
|---------|-------------|---------|
| `./create_agents.sh create [type]` | Create a new specialized agent | `./create_agents.sh create hedge-specialist` |
| `./create_agents.sh list` | List all active sub-agents | `./create_agents.sh list` |
| `./create_agents.sh spawn [type]` | Spawn agent with a task | `./create_agents.sh spawn researcher "Research X"` |
| `./create_agents.sh monitor [type]` | Monitor agent activity | `./create_agents.sh monitor hedge-specialist` |
| `./create_agents.sh kill [type]` | Stop a running agent | `./create_agents.sh kill code-reviewer` |

---

## 🚀 Getting Started Right Now

```bash
cd /home/luxinterior/.openclaw/workspace

# Create your first specialized agent
./create_agents.sh create hedge-specialist

# Give it a task
polyclaw sessions_send --label hedge-specialist "Scan top 10 trending markets for hedge opportunities. Use 95% coverage threshold, Tier 1 only. Log all hedges to database."

# Check the results
./create_agents.sh list
```

---

## 📊 System Architecture

```
┌─────────────────────────────────────────┐
│         Main Session (You)           │
│                                      │
│     ┌────────────────────────────┐    │
│     │  OpenClaw Gateway      │    │
│     │                        │    │
│     └────────────────────────────┘    │
│                │                     │
│         sessions_spawn              │
│                │                     │
│    ┌───────────┬─────────────┐  │
│    │           │             │  │
│ Hedge    Code      Researcher    │  │
│ Specialist  Reviewer              │  │
│ (label)    (label)    (label)    │  │
│    │           │             │  │
│ Isolated   Isolated    Isolated   │  │
│ Sessions  Sessions    Sessions    │  │
│    │           │             │  │
│ └───────────┴─────────────┘  │
│                                      │
└─────────────────────────────────────────┘
```

Each agent:
- Runs in isolated session
- Has its own memory
- Can be called via label
- Persists across main sessions
- Reports results back to main

---

## 💡 Tips

1. **Start Simple** - Create one agent at a time, test it
2. **Be Specific** - Give clear, bounded tasks with context
3. **Set Timeouts** - Use reasonable timeout values for long tasks
4. **Check Results** - Monitor agent output, ask clarifications
5. **Iterate** - Refine agent instructions based on results
6. **Use Agents Together** - Coordinate multiple agents for complex tasks

---

## 🆘 Troubleshooting

### Agent Not Responding
```bash
# Check if agent exists
./create_agents.sh list

# Try sending another message
polyclaw sessions_send --label [agent-label] "Are you there?"
```

### Agent Not Found
```bash
# List all agents to see labels
./create_agents.sh list

# If not found, recreate it
./create_agents.sh create [agent-type]
```

### Session Issues
```bash
# Check session status
polyclaw sessions_list --kinds sub-agent

# View agent history
polyclaw sessions_history --sessionKey [agent-session-key]
```

---

## 📝 Next Steps

1. ✅ Create your first agent: `./create_agents.sh create hedge-specialist`
2. ✅ Test it with a simple task
3. ✅ Create additional agents as needed
4. ✅ Build workflows using multiple agents
5. ✅ Monitor and manage agents

---

_This system allows you to build a team of specialized AI agents that work together efficiently._
