# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working on the Distributed AI Agent Orchestration Platform project.

## Project Overview

This is the **shared workspace** for developing a distributed AI agent orchestration platform that enables process-driven AI-human collaboration. This workspace coordinates development across multiple repositories and demonstrates the "eat your own dogfood" principle by using the vibe-agents framework to orchestrate its own development.

## Repository Purpose

**Shared Workspace Functions**:
- Central coordination hub for all development activities
- Knowledge management and institutional memory
- Process-driven collaboration between AI agents and humans
- Cross-repository project coordination
- Concept development and technical planning

## Project Setup Automation

When users request setup, Claude should:

1. **Clone vibe-agents framework** if missing:
   ```bash
   git clone https://github.com/cloudhippie/vibe-agents.git
   ```

2. **Verify structure** exists:
   ```bash
   ls -la vibe-agents/agents/ vibe-agents/processes/
   ```

3. **Inform user** setup is complete with available agent roles and next steps.

## Agent Roles for This Project

This project uses specialized AI agents working together to develop the platform:


### dev  
**Primary Responsibility**: Implementation across all component repositories
**Scope**: Code implementation in vibe-orchestrator, vibe-agent-server, vibe-workspace-tools, etc.
**Authority**: Implementation decisions within approved architecture
**Collaboration**: Works with architect for design, qa for quality, coordinates across repos
**Process**: Feature implementation, bug fixes, component development, GitHub PR workflows

### docs
**Primary Responsibility**: Documentation and knowledge management
**Scope**: Framework documentation, setup guides, concept refinement, knowledge capture
**Authority**: Documentation standards, information architecture, knowledge organization  
**Collaboration**: Works with all agents to capture learnings, maintains shared workspace
**Process**: Documentation updates, concept evolution, knowledge base maintenance

### qa
**Primary Responsibility**: Testing and quality assurance across all components
**Scope**: Testing strategies, quality standards, integration testing, system validation
**Authority**: Quality gates, testing requirements, release approval
**Collaboration**: Reviews all implementations, defines testing strategies, validates system behavior
**Process**: Test planning, quality validation, system integration testing

### product
**Primary Responsibility**: Roadmap management and feature prioritization
**Scope**: Project roadmap, feature priorities, stakeholder requirements, business value
**Authority**: Feature prioritization, roadmap decisions, requirement clarification
**Collaboration**: Coordinates with all agents, manages project scope, handles escalations
**Process**: Roadmap planning, feature specification, priority management, stakeholder communication

### marketing
**Primary Responsibility**: Brand management, content creation, and community engagement
**Scope**: Framework adoption, developer relations, content strategy, user advocacy
**Authority**: Marketing strategy, brand messaging, community initiatives
**Collaboration**: Works with all agents to communicate value, builds adoption through content
**Process**: Content creation, community building, adoption campaigns, developer relations

### review
**Primary Responsibility**: Code review, quality standards, and technical mentorship
**Scope**: Code quality gates, review processes, standards enforcement, knowledge sharing
**Authority**: Code approval, quality standards, review process definition
**Collaboration**: Reviews all code changes, mentors developers, ensures quality consistency
**Process**: Pull request review, quality gate management, standards documentation, mentorship

## Core Project Structure

### Key Directories
- **`/concepts/`** - Complete vision and architectural concepts (DO NOT MODIFY - these are foundational)
- **`/project/`** - Development planning and roadmap management
- **`/docs/`** - Human-focused documentation (will become website)
- **`/vibe-agents/`** - Natural language framework (no source code, pure templates)

### Component Repositories (Future)
These will be created as development progresses:
- `vibe-orchestrator` - Central coordination service
- `vibe-agent-server` - Agent runtime environment
- `vibe-workspace-tools` - Workspace management utilities
- `vibe-ui` - Management dashboard (if needed)

## Development Workflow

### Process-Driven Collaboration
1. **Issue Creation**: All work starts with GitHub issues in this repository
2. **Agent Assignment**: Issues labeled with agent roles (e.g., `architect`, `dev`)
3. **Cross-Repo Work**: Agents work across multiple repositories as needed
4. **Knowledge Capture**: All learnings and decisions captured in this workspace
5. **Coordination**: Regular check-ins and progress updates through issues/PRs

### Agent Collaboration Patterns

**Sequential Handoffs**:
```
product → architect → dev → create PR → qa → PR merge → marketing 
```

**Consultative Reviews**:
```
dev ←→ architect (ongoing consultation)
qa ←→ all agents (quality feedback)
```

## Framework Application

This project uses the **vibe-agents framework** to coordinate its own development:

### Framework Usage
- **Role Definitions**: Agents follow templates from `/vibe-agents/agents/`
- **Process Templates**: Use workflows from `/vibe-agents/processes/`
- **Best Practices**: Apply patterns from `/vibe-agents/workflows/`
- **Setup Patterns**: Follow guides from `/vibe-agents/setup/`

### Self-Improvement Loop
- **Use Framework**: Apply vibe-agents framework to coordinate development
- **Capture Learnings**: Document what works and what doesn't
- **Improve Framework**: Update templates based on real-world usage
- **Validate Changes**: Test improvements through actual development work

## Technical Guidelines

### Repository Management
- **Primary Coordination**: All coordination happens through this workspace
- **Component Development**: Code lives in dedicated component repositories
- **Knowledge Management**: All shared knowledge captured here
- **Process Evolution**: Continuous improvement of collaboration processes

### Documentation Standards
- **Concept Preservation**: Never modify core concept documents without explicit approval
- **Knowledge Capture**: Document all decisions, learnings, and patterns
- **Cross-Reference**: Link between workspace docs and component implementations
- **Version Control**: Track evolution of processes and knowledge

### Quality Standards
- **Process Adherence**: Follow defined workflows consistently
- **Cross-Agent Communication**: Clear handoffs and collaboration
- **Knowledge Sharing**: Transparent capture of all insights
- **Continuous Improvement**: Regular retrospectives and process refinement

## Commit and PR Guidelines
- For PRs and Commits always use sematic pr spec

## Current Development Focus

### Phase 1: Foundation (Current)
- Transform vibe-agents into natural language framework
- Clean up workspace organization
- Establish clear agent roles and processes
- Plan component repository structure

### Phase 2: Core Implementation (Next)
- Create component repositories
- Implement basic orchestrator
- Build agent server framework
- Establish GitHub integration

### Phase 3: Self-Orchestration (Future)
- Deploy agents to coordinate development
- Full process-driven collaboration
- Cross-repository coordination
- Framework validation through usage

## Key Resources

### Foundational Documents
- [Main Project Concept](/concepts/2025-07-20%20Distributed%20AI%20Agent%20Orchestration%20Platform/01-Main-Project-Concept.md)
- [Agent Framework Concept](/concepts/2025-07-20%20Distributed%20AI%20Agent%20Orchestration%20Platform/02-Agent-Framework-Concept.md)
- [Implementation Roadmap](/project/implementation_roadmap.md)

### Framework Resources
- [Vibe-Agents Framework](/vibe-agents/) - Natural language templates and patterns
- [Agent Philosophy](/vibe-agents/docs/AGENT_PHILOSOPHY.md) - Core principles

### Development Planning
- [Development Concept](/project/development%20concept.md)
- [Project Summary](/PROJECT_SUMMARY.md)

## Agent Coordination Protocols

### Issue Management
- **Labeling**: Use role-based labels (`architect`, `dev`, etc.)
- **Assignment**: Agents self-assign appropriate issues
- **Progress**: Regular updates on issue progress
- **Handoffs**: Clear communication when passing work between agents

### Knowledge Management
- **Decision Records**: Document all significant decisions
- **Learning Capture**: Record insights and patterns discovered
- **Process Evolution**: Update workflows based on experience
- **Cross-Reference**: Link workspace knowledge to component implementations

### Quality Assurance
- **Review Processes**: All work reviewed by appropriate agents
- **Standards Compliance**: Adherence to defined quality standards
- **Integration Testing**: Validation across all components
- **Continuous Feedback**: Regular process improvement cycles

Remember: This workspace coordinates the development of a system that will revolutionize how AI agents and humans collaborate. Every decision and implementation should advance this vision while demonstrating the principles we're building into the platform.