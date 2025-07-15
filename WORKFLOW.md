# Vibe Space Development Workflow

This document describes the development workflow for the Vibe Space project using the Vibe Agents framework.

## Overview

Our workflow follows GitOps principles with AI agents and human developers collaborating through Git-based processes.

## Workflow Stages

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│ Planning │───▶│ Develop │───▶│ Review  │───▶│ Testing │───▶│ Deploy  │
└─────────┘    └─────────┘    └─────────┘    └─────────┘    └─────────┘
     ▲                                                              │
     └──────────────────────────────────────────────────────────────┘
                                Feedback Loop
```

## 1. Planning Stage

### Issue Creation
- Product Agent or humans create issues
- Issues tagged with appropriate labels
- Assigned to relevant agents or developers

### Issue Template
```markdown
## Summary
Brief description of the task

## Acceptance Criteria
- [ ] Specific requirement 1
- [ ] Specific requirement 2

## Agent Assignment
- Primary: @development-agent
- Review: @review-agent
- QA: @qa-agent

## Dependencies
- Blocked by: #123
- Blocks: #125
```

## 2. Development Stage

### Branch Creation
```bash
# Feature branch
git checkout -b feature/issue-123-description

# Bug fix branch  
git checkout -b fix/issue-456-description
```

### Commit Convention
```
<type>(<scope>): <subject>

<body>

Agent: <agent-name>
Issue: #<number>
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `refactor`: Code refactoring
- `test`: Testing
- `chore`: Maintenance

### Development Checklist
- [ ] Create feature branch
- [ ] Implement changes
- [ ] Add/update tests
- [ ] Update documentation
- [ ] Self-review changes
- [ ] Create pull request

## 3. Review Stage

### Pull Request Process

1. **Create PR**
   ```bash
   gh pr create \
     --title "feat: implement feature X" \
     --body-file .github/pull_request_template.md \
     --assignee @me \
     --reviewer @review-agent
   ```

2. **PR Template**
   ```markdown
   ## Description
   Brief description of changes
   
   ## Type of Change
   - [ ] Bug fix
   - [ ] New feature
   - [ ] Documentation update
   
   ## Testing
   - [ ] Tests added/updated
   - [ ] All tests passing
   
   ## Checklist
   - [ ] Code follows style guide
   - [ ] Documentation updated
   - [ ] No security issues
   
   ## Related Issues
   Closes #123
   ```

3. **Review Criteria**
   - Code quality and style
   - Test coverage
   - Documentation completeness
   - Security considerations
   - Performance impact

### Review Agent Workflow
```bash
# Check out PR
gh pr checkout 123

# Run automated checks
npm run lint
npm run test
npm run security-check

# Add review
gh pr review 123 --approve --body "LGTM! Minor suggestions added."
```

## 4. Testing Stage

### QA Agent Responsibilities
- Validate functionality
- Check documentation accuracy
- Verify examples work
- Test edge cases
- Performance testing

### Testing Checklist
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Documentation examples verified
- [ ] No broken links
- [ ] Performance benchmarks met

## 5. Deployment Stage

### Merge Strategy
- Squash and merge for feature branches
- Merge commit for release branches
- Rebase for small fixes

### Post-Merge Actions
1. Delete feature branch
2. Update project board
3. Close related issues
4. Notify stakeholders

## Agent Collaboration Patterns

### Sequential Handoff
```
Development Agent → Review Agent → QA Agent → Deploy
```

### Parallel Work
```
Development Agent ─┬─→ Documentation Agent
                  └─→ QA Agent (test planning)
```

### Escalation Path
```
Agent → Senior Agent → Human Developer → Project Lead
```

## Communication Protocols

### Issue Comments
- Use for task updates
- Ask for clarification
- Report blockers

### PR Comments
- Code-specific feedback
- Suggest improvements
- Approve/request changes

### Commit Messages
- Agent handoffs
- Status updates
- Important decisions

## Automation

### GitHub Actions Workflows

1. **PR Validation**
   ```yaml
   name: PR Validation
   on: pull_request
   
   jobs:
     validate:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         - name: Run checks
           run: |
             npm run lint
             npm run test
   ```

2. **Auto-merge**
   ```yaml
   name: Auto-merge
   on:
     pull_request_review:
       types: [submitted]
   
   jobs:
     merge:
       if: github.event.review.state == 'approved'
       runs-on: ubuntu-latest
       steps:
         - name: Merge PR
           uses: pascalgn/merge-action@v0.1.4
   ```

## Conflict Resolution

### Merge Conflicts
1. Agent attempts automatic resolution
2. If complex, create issue for human review
3. Document resolution in PR

### Decision Conflicts
1. Agents discuss in PR/issue
2. Escalate to human if needed
3. Document decision rationale

## Best Practices

### For All Contributors
1. **Atomic Commits**: One logical change per commit
2. **Clear Messages**: Descriptive commit and PR messages
3. **Regular Updates**: Keep PRs and issues updated
4. **Test First**: Write tests before implementation
5. **Document Changes**: Update docs with code changes

### For AI Agents
1. **Explicit Handoffs**: Clear agent-to-agent communication
2. **Fail Gracefully**: Escalate when uncertain
3. **Learn Patterns**: Follow existing code style
4. **Track Progress**: Update issues regularly
5. **Collaborate**: Work with other agents effectively

### For Human Developers
1. **Guide Agents**: Provide clear requirements
2. **Review Thoroughly**: Check agent-generated code
3. **Mentor**: Help agents improve
4. **Document**: Capture decisions and rationale
5. **Trust but Verify**: Allow automation with checks

## Metrics and Monitoring

### Key Metrics
- PR turnaround time
- Issue resolution time
- Test coverage
- Documentation coverage
- Agent efficiency

### Dashboards
- GitHub Insights
- Custom metrics dashboard
- Agent activity reports
- Quality metrics

## Continuous Improvement

### Weekly Reviews
- Analyze metrics
- Identify bottlenecks
- Propose improvements
- Update workflows

### Retrospectives
- What worked well?
- What needs improvement?
- Action items
- Process updates

## Emergency Procedures

### Production Issues
1. Create high-priority issue
2. Assign to available agent/human
3. Fast-track PR process
4. Document incident

### Agent Failures
1. Automatic fallback to human
2. Debug agent issue
3. Update agent configuration
4. Resume normal workflow

## Appendix

### Useful Commands

```bash
# Check PR status
gh pr status

# List agent commits
git log --author="*-agent@*" --oneline

# Find issues assigned to agents
gh issue list --assignee "@development-agent"

# Check workflow runs
gh run list --workflow=ci.yml
```

### Resources
- [GitHub CLI Documentation](https://cli.github.com/)
- [Git Best Practices](https://git-scm.com/book)
- [Vibe Agents Philosophy](./vibe-agents/docs/AGENT_PHILOSOPHY.md)