# Getting Started

This guide will help you set up the Distributed AI Agent Orchestration Platform workspace and get started with agent-driven development.

## Prerequisites

- **Git** - Version control system
- **Claude Code CLI** - Anthropic's CLI tool ([Installation Guide](https://docs.anthropic.com/en/docs/claude-code))
- **GitHub Account** - For repository access and collaboration
- **Terminal/Command Line** - Basic familiarity with command line operations

## Quick Setup

1. **Clone the workspace**
   ```bash
   git clone https://github.com/felixboehm/vibe-space.git
   cd vibe-space
   ```

2. **Start Claude Code**
   ```bash
   claude
   ```

3. **Request automated setup**
   ```
   Setup this project from scratch, including cloning the vibe-agents framework
   ```

Claude will automatically clone the [vibe-agents framework](https://github.com/cloudhippie/vibe-agents) and configure the workspace for agent-driven development.

## Manual Setup (Alternative)

If you prefer manual setup:

```bash
# Clone the vibe-agents framework
git clone https://github.com/cloudhippie/vibe-agents.git

# Verify workspace structure
ls -la
```

Your workspace should include:
```
vibe-space/
├── concepts/                 # Core project concepts and vision
├── project/                  # Development planning and roadmap
├── docs/                     # Human-focused documentation
├── vibe-agents/             # Natural language agent framework (cloned)
├── CLAUDE.md                # Claude Code guidance and instructions
└── README.md                # Project overview and entrypoint
```

## Next Steps

1. **Explore the Vision**: Start with [concepts](/concepts/) to understand the project goals
2. **Review the Roadmap**: Check [project/implementation_roadmap.md](/project/implementation_roadmap.md)
3. **Work with Agents**: Use Claude Code with agent roles (architect, dev, docs, qa, product)
4. **Follow Processes**: Reference [vibe-agents framework](/vibe-agents/) for structured workflows

## Working with Claude

### Agent Roles Available

- **architect**: `Work as the architect agent to design system architecture`
- **dev**: `Work as the dev agent to implement features`
- **docs**: `Work as the docs agent to maintain documentation`
- **qa**: `Work as the qa agent to ensure quality`
- **product**: `Work as the product agent to manage roadmap`

### Common Commands

```bash
# Start in workspace directory
cd vibe-space
claude

# Request agent-specific work
"Work as the architect agent to review the system design"
"Work as the dev agent to implement the orchestrator component"
"Work as the docs agent to update the setup guide"
```

## Development Workflow

This project demonstrates process-driven collaboration:

- **Issue Creation**: All work starts with GitHub issues
- **Agent Assignment**: Issues labeled with agent roles
- **Cross-Repo Work**: Agents coordinate across multiple repositories
- **Knowledge Capture**: All learnings documented in this workspace

## Project Phases

### Phase 1: Foundation (Current)
- Transform vibe-agents into natural language framework
- Establish agent roles and processes
- Plan component repository structure

### Phase 2: Core Implementation (Next)
- Create component repositories (vibe-orchestrator, vibe-agent-server)
- Implement basic orchestration capabilities
- Establish GitHub integration

### Phase 3: Self-Orchestration (Future)
- Deploy agents to coordinate development
- Full process-driven collaboration
- Framework validation through real usage

## Troubleshooting

### Common Issues

1. **Repository not found**
   ```bash
   # Verify GitHub access
   git ls-remote https://github.com/felixboehm/vibe-space.git
   git ls-remote https://github.com/cloudhippie/vibe-agents.git
   ```

2. **Claude Code not found**
   ```bash
   # Install Claude Code CLI (follow Anthropic documentation)
   # Verify installation
   claude --version
   ```

3. **Permission issues**
   ```bash
   # Check directory permissions
   ls -la
   # Ensure you have write access to the workspace
   ```

## Getting Help

- **Claude Code Documentation**: https://docs.anthropic.com/en/docs/claude-code
- **Project Issues**: https://github.com/felixboehm/vibe-space/issues
- **Vibe-Agents Framework**: https://github.com/cloudhippie/vibe-agents

## Advanced Setup

### Multiple Workspace Pattern

For advanced users working on multiple projects:

```bash
# Create a dedicated directory for all vibe-related projects
mkdir ~/vibe-projects
cd ~/vibe-projects

# Clone the workspace
git clone https://github.com/felixboehm/vibe-space.git
cd vibe-space

# Future component repositories will be cloned alongside
# vibe-projects/
# ├── vibe-space/           # This workspace
# ├── vibe-orchestrator/    # Future: Core orchestration service
# ├── vibe-agent-server/    # Future: Agent runtime
# └── vibe-workspace-tools/ # Future: Workspace utilities
```

### Development Environment

The project is designed to work with:
- **Any text editor/IDE** - Claude Code works independently
- **Any operating system** - macOS, Linux, Windows
- **Any terminal** - bash, zsh, fish, PowerShell