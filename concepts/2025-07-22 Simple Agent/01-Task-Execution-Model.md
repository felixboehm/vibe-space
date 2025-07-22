# Simple Agent Task Execution Model

## Overview
This concept defines a straightforward model for autonomous dev agents working with GitHub as both the tasklist (issues) and review space (pull requests). The agent operates on a simple loop: check for work, execute if available, report if blocked.

## Core Principles
1. **Single Task Focus**: Agent works on one task at a time to completion
2. **Explicit Readiness**: Tasks must meet all "definition of ready" criteria
3. **Quality Gates**: All work goes through PR review process
4. **Continuous Feedback**: Regular updates to issues and PRs
5. **Human Escalation**: Clear paths for human intervention

## Component Mapping

### Tasklist: GitHub Issues
- **Task Definition**: Issue body contains requirements
- **Status Tracking**: Labels indicate state (ready, in-progress, blocked)
- **Assignment**: Issue assignee shows current owner
- **Priority**: Labels, milestones, or issue order
- **Dependencies**: Referenced in definition of ready

### Review Space: Pull Requests
- **Code Changes**: Feature branch with implementation
- **Documentation**: Updated docs in same PR
- **Test Results**: CI/CD status checks
- **Review Process**: Human and automated reviews
- **Approval Gates**: Required reviews before merge

### Data Stores
- **Primary**: Git repository (code, configs, docs)
- **Secondary**: External APIs, databases as needed
- **Artifacts**: Build outputs, test reports
- **Knowledge**: README, wikis, past issues

## Agent Workflow

### 1. Task Discovery (Cron Triggered)
```
Every N minutes:
- Query GitHub for open, unassigned issues
- Filter by "ready" label or evaluate readiness
- Sort by priority/age/dependencies
- Select highest priority ready task
```

### 2. Readiness Validation
```
For each candidate task:
- Parse definition of ready from issue
- Check dependent issues are closed
- Verify required resources available
- Validate agent has necessary permissions
- Confirm no blocking conditions
```

### 3. Task Execution
```
When task is ready:
- Claim issue (assign self, add label)
- Create feature branch
- Implement requirements
- Run tests and validations
- Update documentation
- Commit with issue references
```

### 4. Review Submission
```
When implementation complete:
- Push branch to GitHub
- Create PR with:
  - Summary of changes
  - Link to issue
  - Test results
  - Demo/screenshots if applicable
- Request reviews per repository rules
- Monitor PR for feedback
```

### 5. Completion Handling
```
When PR approved and merged:
- Close related issue
- Clean up feature branch
- Report completion metrics
- Check for follow-up tasks
- Return to discovery phase
```

## Definition Structures

### Definition of Ready Example
```yaml
ready_when:
  all_of:
    - issue: "#123 is closed"  
    - issue: "#124 is closed"
    - label: "approved"
    - file_exists: "api/spec/v2.yaml"
    - check: "CI is green on main"
```

### Definition of Done Example
```yaml
done_when:
  all_of:
    - code: "feature implemented"
    - tests: "unit tests added"
    - tests: "all tests passing"
    - docs: "README updated"
    - docs: "API docs updated"
    - pr: "created and linked"
    - pr: "description includes demo"
    - review: "approved by 2 reviewers"
```

## Blocked Task Reporting

When no tasks are ready, agent reports on top 5 tasks:
```
Task #127: Blocked on issue #125 (not closed)
Task #128: Missing approval label
Task #129: Waiting for api/spec/v2.yaml
Task #130: Blocked on issue #125 and #126
Task #131: CI failing on main branch
```

## Error Handling

1. **API Failures**: Retry with exponential backoff
2. **Merge Conflicts**: Report in PR, request human help
3. **Test Failures**: Document in PR, attempt fixes
4. **Missing Dependencies**: Report in issue comments
5. **Permission Errors**: Escalate to repository admin

## Success Metrics

- **Task Completion Rate**: Successfully merged PRs
- **Readiness Accuracy**: Ready tasks actually completable
- **Cycle Time**: Issue creation to PR merge
- **Quality Metrics**: Test coverage, review feedback
- **Blocker Resolution**: Time to unblock tasks

## Future Enhancements

1. **Multi-Agent Coordination**: Agents communicate through issues
2. **Learning System**: Improve estimates and decisions over time
3. **Advanced Scheduling**: Consider agent specializations
4. **Dependency Resolution**: Automatically work on blockers
5. **Performance Optimization**: Parallel work where possible

## Relationship to Full Orchestration Model

This simple model provides a foundation that can evolve into the more sophisticated orchestration patterns described in the main platform concepts. Starting with this approach allows for:

- **Immediate Value**: Agents can start working with minimal infrastructure
- **Gradual Complexity**: Add orchestration layers as needs grow
- **Proven Patterns**: Build on familiar GitHub workflows
- **Easy Debugging**: Simple model is easier to troubleshoot
- **Natural Evolution**: Can transition to advanced patterns when ready

The abstractions (tasklist, review space, data store) remain consistent, allowing for future migration to more complex orchestration approaches without fundamental redesign.