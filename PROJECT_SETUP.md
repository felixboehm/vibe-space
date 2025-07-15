# Vibe Space Project Setup

This project uses the Vibe Agents framework for AI-assisted project management and development.

## Project Overview

**Project Name**: Vibe Space - VibeTunnel Documentation and Agent Framework Development
**Project Type**: Documentation & Framework Development
**Primary Repository**: https://github.com/felixboehm/vibe-space
**Agent Framework**: https://github.com/cloudhippie/vibe-agents

## Project Structure

```
vibe-space/
├── docs/                      # VibeTunnel documentation
├── vibe-agents/              # Agent framework (submodule)
│   ├── docs/                 # Framework documentation
│   ├── issues/               # Issue templates
│   └── templates/            # Agent templates
├── scripts/                  # Utility scripts
├── .github/                  # GitHub configuration
│   ├── workflows/           # CI/CD workflows
│   └── ISSUE_TEMPLATE/      # Issue templates
├── PROJECT_SETUP.md         # This file
├── WORKFLOW.md              # Development workflow
├── ROADMAP.md               # Project roadmap
└── CLAUDE.md                # AI agent instructions
```

## Active Agents

### 1. Development Agent
- **Role**: Primary development and implementation
- **Responsibilities**: 
  - Code implementation
  - Technical documentation
  - Infrastructure setup
  - Tool integration

### 2. Documentation Agent
- **Role**: Documentation management
- **Responsibilities**:
  - Maintain VibeTunnel docs
  - Create agent guides
  - Update README files
  - Ensure consistency

### 3. Product Agent
- **Role**: Feature planning and roadmap
- **Responsibilities**:
  - Feature prioritization
  - Roadmap management
  - User story creation
  - Stakeholder communication

### 4. QA Agent
- **Role**: Quality assurance
- **Responsibilities**:
  - Testing documentation
  - Validate procedures
  - Check broken links
  - Verify examples work

## Repository Configuration

### Main Branches
- `main` - Production-ready documentation
- `develop` - Integration branch
- `feature/*` - Feature branches
- `fix/*` - Bug fix branches

### Protected Branch Rules
- `main` requires:
  - PR reviews
  - CI checks passing
  - No direct pushes

## Communication Channels

### GitHub
- **Issues**: Task tracking and discussions
- **PRs**: Code reviews and changes
- **Projects**: Overall project management

### Agent Handoffs
- Use commit messages for handoff
- Tag relevant agents in PRs
- Update issue status accordingly

## Development Workflow

See [WORKFLOW.md](./WORKFLOW.md) for detailed workflow documentation.

## Getting Started

### For Human Developers
1. Clone the repository with submodules:
   ```bash
   git clone --recursive git@github.com:felixboehm/vibe-space.git
   cd vibe-space
   ```

2. Review project documentation:
   - Read this PROJECT_SETUP.md
   - Check WORKFLOW.md
   - Review CLAUDE.md for AI context

3. Set up development environment:
   - Install required tools
   - Configure Git identity
   - Set up GitHub CLI

### For AI Agents
1. Read CLAUDE.md for project context
2. Check assigned issues in GitHub
3. Follow GitOps workflow for changes
4. Coordinate with other agents via PRs

## Project Goals

### Phase 1: Foundation (Current)
- [x] Set up vibe-agents framework
- [x] Create server setup documentation  
- [x] Document GitOps workflow
- [ ] Initialize project structure
- [ ] Define agent roles

### Phase 2: Implementation
- [ ] Deploy first AI agent
- [ ] Establish agent workflows
- [ ] Create monitoring dashboards
- [ ] Document best practices

### Phase 3: Expansion
- [ ] Multi-agent coordination
- [ ] Advanced GitOps patterns
- [ ] Performance optimization
- [ ] Security hardening

## Success Metrics

- Documentation completeness
- Agent task completion rate
- PR turnaround time
- Issue resolution speed
- Code quality metrics

## Links and Resources

- [Vibe Agents Philosophy](./vibe-agents/docs/AGENT_PHILOSOPHY.md)
- [Agent Server Setup](./vibe-agents/docs/AGENT_SERVER_SETUP.md)
- [GitOps Workflow](./vibe-agents/docs/GITOPS_WORKFLOW.md)
- [VibeTunnel Documentation](./README.md)