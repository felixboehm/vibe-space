# Implementation Plan for Dev Agent with GitHub Integration

## Overview
This document outlines the implementation steps for creating a simple, cron-triggered dev agent that works autonomously with GitHub issues and pull requests. The plan focuses on practical steps to get a working agent without complex orchestration infrastructure.

## Requirements for Agent Operation

### System Requirements
The agent server must have the following configured:

**Access Rights**
- Full sudo access for package installation and system configuration
- Read/write permissions to `/work/dev/` directory structure
- Ability to create and manage subdirectories for issue workspaces
- Permission to run cron jobs and background processes

**GitHub Access**
- Personal Access Token or GitHub App with permissions:
  - Repository access (read/write)
  - Issues (read/write) 
  - Pull requests (read/write)
  - Actions (read)
  - Metadata (read)
- SSH key configured for Git operations (optional but recommended)

**Git Configuration**
- Git user name configured: `git config --global user.name "Dev Agent"`
- Git user email configured: `git config --global user.email "dev-agent@example.com"`
- SSH key added to ssh-agent if using SSH
- Git credential helper configured for HTTPS if not using SSH

**Development Tools**
- Git (latest stable version)
- GitHub CLI (`gh`) for API operations
- Programming language runtimes as needed by projects
- Build tools required by projects (npm, pip, maven, etc.)
- Testing frameworks as specified by projects

**Claude Code Requirements**
- Claude Code installed and activated
- Valid API key configured
- Sufficient context window for project operations

### Setup Validation vs Implementation

**Agent Setup (Validation Only)**
The agent setup script should only verify that requirements are met:
- Check for required tools and versions
- Validate GitHub access and permissions
- Verify file system permissions
- Confirm Git configuration
- Test Claude Code availability

**Project Setup (Implementation)**
The project-specific setup implements actual configuration:
- Install project-specific dependencies
- Configure project environment variables
- Set up project-specific Git hooks
- Initialize project databases or services
- Configure IDE settings if needed

## Phase 1: Environment Setup

### 1.1 Agent Workspace Structure
Create the following directory structure:
```
/work/dev/
├── CLAUDE.md                    # Dev agent behavioral instructions
├── issue-123/                   # Issue-specific workspace
│   ├── project-repo/           # Cloned repository
│   ├── logs/                   # Agent execution logs
│   └── .metadata.json          # Issue state and context
├── issue-456/                   # Another issue workspace
└── _archive/                    # Closed issues (auto-cleaned)
```

### 1.2 Dev Agent CLAUDE.md
The `/work/dev/CLAUDE.md` file defines agent behavior:
```markdown
# Dev Agent Instructions

You are a development agent responsible for implementing GitHub issues autonomously.

## Core Behaviors
1. Work on one issue at a time to completion
2. Create dedicated workspace for each issue: /work/dev/issue-{number}/
3. Maintain workspace until issue is closed
4. Clean up workspaces for closed issues daily

## Workflow
1. Check for PR improvements first (failing tests, review comments)
2. Find highest priority ready issue if no PR work
3. Create issue workspace and clone repository
4. Implement according to issue requirements
5. Create PR with auto-close reference
6. Monitor PR until merged

## Quality Standards
- Write comprehensive tests for all new code
- Update documentation for any API changes
- Follow project coding conventions
- Ensure CI passes before marking complete

## Communication
- Comment implementation plan on issue before starting
- Update issue with progress every significant milestone
- Escalate blockers immediately with clear explanation
- Document decisions in PR description
```

### 1.3 Cron Job Configuration
Set up cron to trigger agent:
```bash
# Run every minute
* * * * * cd /work/dev && claude-code "Check for work and execute highest priority task"

# Daily cleanup of closed issue workspaces
0 2 * * * cd /work/dev && claude-code "Clean up workspaces for closed issues"
```

### 1.4 Initial Setup Script
Create setup validation script that only checks requirements:
```bash
#!/bin/bash
# validate-agent-setup.sh - Validates but doesn't modify

echo "Validating dev agent requirements..."

# Check directory permissions
test -w /work/dev || echo "ERROR: /work/dev not writable"

# Check tools
command -v git || echo "ERROR: git not installed"
command -v gh || echo "ERROR: GitHub CLI not installed"
command -v claude-code || echo "ERROR: Claude Code not installed"

# Check Git config
git config --global user.name || echo "ERROR: Git user.name not set"
git config --global user.email || echo "ERROR: Git user.email not set"

# Check GitHub access
gh auth status || echo "ERROR: GitHub CLI not authenticated"

# All checks passed
echo "Agent setup validation complete"
```

## Phase 2: Task Management Integration

### 2.1 GitHub Issue Configuration
- Define standard labels:
  - `agent-ready`: Task ready for agent pickup
  - `agent-in-progress`: Agent currently working
  - `agent-blocked`: Task cannot proceed
  - Priority labels (p0, p1, p2)
- Create issue templates with structured format
- Document definition of ready/done standards
- Set up project board for visibility

### 2.2 Issue Template Structure
```markdown
## Task Description
[Clear description of what needs to be done]

## Definition of Ready
**Dependencies**
- [ ] Issue #123 is closed
- [ ] PR #456 is merged

**Resources**
- [ ] API specification available at `/docs/api/v2.yaml`
- [ ] Test data available in `/test/fixtures/`
- [ ] Database schema documented

**Approvals**
- [ ] Design has been approved
- [ ] Security review is complete
- [ ] Product owner has signed off

## Definition of Done
**Implementation**
- [ ] Feature works according to specifications
- [ ] All acceptance criteria are met
- [ ] Edge cases are handled gracefully
- [ ] Code follows project conventions

**Testing**
- [ ] Unit tests written with >80% coverage
- [ ] Integration tests are passing
- [ ] Manual testing completed
- [ ] Performance impact assessed

**Documentation**
- [ ] README updated with new functionality
- [ ] API documentation generated/updated
- [ ] Changelog entry added
- [ ] Configuration examples provided

**Review Process**
- [ ] PR created with detailed description
- [ ] At least 1 reviewer has approved
- [ ] All CI/CD checks are passing
- [ ] No unresolved comments

## Technical Details
[Any additional context, constraints, or guidance]
```

### 2.3 Priority and Scheduling
- Use milestones for release planning
- Priority labels for task ordering
- Due dates in issue metadata
- Dependencies tracked in definition of ready

## Phase 3: Agent Behavior Implementation

### 3.1 Workspace Management Strategy
Each issue gets its own isolated workspace:
```bash
# When starting work on issue #123
mkdir -p /work/dev/issue-123/logs
cd /work/dev/issue-123
git clone <repository-url> project-repo
cd project-repo

# Save issue metadata
cat > ../.metadata.json << EOF
{
  "issue_number": 123,
  "started_at": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "repository": "<repo-name>",
  "branch": "feature/issue-123"
}
EOF
```

### 3.2 Task Discovery Process
Following the CLAUDE.md instructions:
1. First check for open PRs needing attention:
   - PRs with failing tests
   - PRs with review comments
   - PRs with merge conflicts
2. If no PR work, find ready issues:
   - Query GitHub API for open, unassigned issues
   - Check definition of ready for each
   - Select highest priority ready task

### 3.3 Readiness Validation Logic
Parse natural language definition of ready:
- Check each checkbox item in "Dependencies" section
- Verify "Resources" items are available
- Confirm "Approvals" are complete
- Generate report if any items unchecked

### 3.4 Task Claiming and Planning
When claiming issue #123:
```bash
# Navigate to issue workspace
cd /work/dev/issue-123/project-repo

# Assign issue and update labels
gh issue edit 123 --add-assignee "@me"
gh issue edit 123 --remove-label "agent-ready" --add-label "agent-in-progress"

# Post implementation plan
gh issue comment 123 --body "Starting work on this issue.

Implementation Plan:
1. Set up issue workspace at /work/dev/issue-123
2. Create feature branch: feature/issue-123
3. [Specific implementation steps based on requirements]
4. Write comprehensive unit tests
5. Update documentation
6. Create PR with auto-close reference

Estimated completion: 2-4 hours"
```

### 3.5 Work Execution Flow
Execute within issue workspace:
```bash
cd /work/dev/issue-123/project-repo

# Create feature branch
git checkout -b feature/issue-123

# Run project setup if needed
./scripts/setup.sh  # or npm install, etc.

# Implement changes
# - Follow requirements from issue
# - Write tests alongside code
# - Update docs as needed

# Validate work
npm test  # or appropriate test command
npm run lint  # or appropriate lint command

# Commit with issue reference
git add .
git commit -m "feat: implement feature for issue #123

- Add new functionality as specified
- Include comprehensive unit tests  
- Update API documentation

Refs #123"
```

### 3.6 Pull Request Creation
Create PR with full context:
```bash
# Push to GitHub
git push -u origin feature/issue-123

# Create PR with auto-close
gh pr create \
  --title "feat: [Issue #123] Clear description of change" \
  --body "## Summary
Implementation of features requested in #123.

## Changes
- Detailed list of changes made
- Test coverage added
- Documentation updates

## Definition of Done Checklist
- [x] Feature implemented according to spec
- [x] Unit tests with 85% coverage
- [x] All tests passing
- [x] Documentation updated
- [ ] Review approved
- [ ] CI/CD passing

## Testing
Describe how to test the changes.

## Screenshots
[If applicable]

Fixes #123" \
  --assignee "@me" \
  --label "needs-review"
```

## Phase 4: Monitoring and Reporting

### 4.1 Blocked Task Reporting
When no tasks are ready:
- Analyze top 5 high-priority issues
- For each issue, identify blockers:
  - Missing dependencies
  - Failing ready conditions
  - Required approvals
- Comment on each blocked issue
- Create summary report
- Post to designated Slack channel or issue

### 4.2 Progress Tracking
- Update issue comments with progress
- Log execution metrics:
  - Task start/end times
  - Success/failure rates
  - Common failure reasons
- Track PR approval times
- Monitor merge success rates

### 4.3 Notification System
- Issue comment updates
- PR creation notifications
- Blocked task alerts
- Error escalation to humans
- Daily summary reports

## Phase 5: Workspace and State Management

### 5.1 Workspace Lifecycle
**Active Workspace Management**
- One workspace per issue: `/work/dev/issue-{number}/`
- Workspace persists throughout issue lifecycle
- Contains cloned repo, logs, and metadata
- Allows resuming work after interruptions

**Closed Issue Cleanup**
Daily cron job to clean closed issues:
```bash
#!/bin/bash
# Clean up workspaces for closed issues
for dir in /work/dev/issue-*/; do
  issue_num=$(basename "$dir" | sed 's/issue-//')
  if gh issue view "$issue_num" --json state -q '.state' | grep -q "CLOSED"; then
    echo "Archiving workspace for closed issue #$issue_num"
    mv "$dir" "/work/dev/_archive/"
  fi
done

# Clean archives older than 30 days
find /work/dev/_archive/ -maxdepth 1 -type d -mtime +30 -exec rm -rf {} \;
```

### 5.2 State Persistence
**Metadata Tracking**
Each workspace contains `.metadata.json`:
```json
{
  "issue_number": 123,
  "repository": "owner/repo",
  "started_at": "2024-01-15T10:00:00Z",
  "branch": "feature/issue-123",
  "status": "implementing",
  "last_action": "Running tests",
  "pr_number": null
}
```

**Resume Capability**
Agent can resume work by:
1. Check existing workspaces on startup
2. Read metadata to understand state
3. Continue from last action
4. Update issue with resume notice

### 5.3 Error Handling
**Workspace-Specific Logging**
All operations logged to workspace:
```bash
LOG_FILE="/work/dev/issue-123/logs/$(date +%Y%m%d).log"
echo "[$(date)] Starting implementation" >> "$LOG_FILE"
```

**Failure Recovery**
- Git conflicts: Attempt auto-merge, escalate if complex
- Test failures: Re-run, analyze logs, document in PR
- Build failures: Check dependencies, retry, escalate
- API limits: Exponential backoff with state preservation

### 5.4 Human Escalation
When blocked, update issue with clear status:
```markdown
🚨 **Agent Status: Blocked**

I'm unable to proceed with this issue due to:
- Merge conflict in `src/main.js` that requires human judgment
- Failing test that seems unrelated to my changes
- Missing dependency specification

**Current State:**
- Workspace: `/work/dev/issue-123/`
- Branch: `feature/issue-123` 
- Last action: Running tests
- Error details in: `logs/20240115.log`

**Suggested Resolution:**
1. Review the merge conflict in the PR
2. Check if the failing test is flaky
3. Clarify the missing dependency

@maintainer - Please advise on how to proceed.
```

## Phase 6: Testing and Validation

### 6.1 Agent Testing
- Test against sample repository
- Verify all GitHub API interactions
- Test error scenarios
- Validate cron job reliability
- Check resource usage

### 6.2 Integration Testing
- Test with real repository
- Verify CI/CD integration
- Test with various issue formats
- Validate PR review process
- Check notification delivery

## Phase 7: Deployment and Operations

### 7.1 Production Deployment
- Deploy to production agent server
- Configure production credentials
- Set up monitoring and alerts
- Enable cron job
- Document runbooks

### 7.2 Operational Procedures
- Log rotation and retention
- Credential rotation schedule
- Performance monitoring
- Incident response procedures
- Upgrade procedures

## Success Criteria

1. **Reliability**: Agent runs every minute without failures
2. **Workspace Management**: Clean workspace per issue, proper cleanup
3. **Accuracy**: 95% of ready tasks successfully completed  
4. **Quality**: All PRs pass review without major rework
5. **Visibility**: Clear status on all tasks and blockers
6. **Performance**: Tasks completed within reasonable timeframes

## Future Iterations

1. **Multi-Repository Support**: Work across multiple repos
2. **Parallel Task Execution**: Handle multiple simple tasks
3. **Smart Dependencies**: Automatically resolve blockers
4. **Learning System**: Improve based on past performance
5. **Advanced Scheduling**: Time-based and capacity planning

This implementation plan provides a practical path to a working agent system that can deliver immediate value while laying groundwork for future enhancements.