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
Every 1 minute:
- Query Github open PRs for reviews, failing tests, comments, conflicts. Improve the PR. 
- Otherwise Query GitHub for open, unassigned issues
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
- Claim issue (assign self, set issue status to doing)
- Add an implementation plan as comment in the issue
- Create feature branch
- Implement requirements and unit tests
- Run tests and validations
- Update documentation
- Create a PR with issue references to autoclose issue on merge
- Add implementation description as context to the PR
```

### 4. Review Submission
```
When implementation complete:
- Push branch to GitHub
- Create PR with:
  - Summary of changes
  - Link to issue, using auto-close (Fixes #53)
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

### Definition of Ready
A task is ready when all of the following conditions are met:

**Dependencies**
- All prerequisite issues (e.g., #123, #124) are closed
- Required approvals are in place (indicated by "approved" label)

**Resources** 
- All necessary files and specifications are available (e.g., api/spec/v2.yaml exists)
- External systems and data sources are accessible
- Required tools and services are operational

**Environment**
- CI/CD pipeline is green on the main branch
- Test environments are available and configured
- No blocking infrastructure issues

**Clarity**
- Task requirements are clear and unambiguous
- Acceptance criteria are well-defined
- Technical approach is understood or discoverable

### Definition of Done
A task is complete when all of the following are satisfied:

**Implementation**
- Feature is fully implemented according to requirements
- Code follows project standards and conventions
- All edge cases and error scenarios are handled

**Testing**
- Unit tests are written with appropriate coverage
- All tests (unit, integration, e2e) are passing
- Security and performance implications are validated

**Documentation**
- README is updated with new features or changes
- API documentation reflects any interface changes
- Inline code documentation explains complex logic
- Changelog or release notes are updated

**Review Process**
- Pull request is created with clear description
- PR links to original issue using auto-close syntax (Fixes #123)
- Implementation approach is documented in PR description
- At least one reviewer has approved the changes
- All review feedback has been addressed

**Demonstration**
- Working demo or screenshots are provided where applicable
- Acceptance criteria can be verified in the PR
- Any deployment or configuration steps are documented

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