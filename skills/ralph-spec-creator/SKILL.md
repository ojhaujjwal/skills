---
name: ralph-spec-creator
description: Interactive planning skill for creating pending specs compatible with ralph-loop. Use this skill ONLY during active planning conversations - ask clarifying questions, explore options, and finalize specs with all questions answered. Do NOT invoke during ralph-loop execution.
---

# Ralph Spec Creator

This skill creates detailed, actionable specs in `specs/pending/` that are compatible with ralph-loop script for autonomous execution.

## Important: This Skill is Interactive-Only

**DO NOT use this skill during ralph-loop execution.** This skill requires back-and-forth conversation with the user to clarify requirements, explore options, and finalize decisions.

**When to use:**
- When user says "create a spec for..." or "plan a feature"
- When user wants to plan work before running ralph-loop is started.
- When user asks to think through requirements for new work

**When NOT to use:**
- During ralph-loop execution (ralph-loop reads specs, doesn't plan them)
- When you're in an autonomous loop
- When you can't ask follow-up questions

**Workflow:**
1. User: "Create a spec for X"
2. You: [Use this skill to ask questions, explore options]
3. User: [Answers questions, confirms approach]
4. You: [Create finalized spec in specs/pending/ with all questions answered]
5. User: "ralph-loop.sh X" → autonomous execution begins

## Overview

This is a **planning-mode skill**. Instead of jumping straight to implementation, you guide the user through a structured planning process:

1. **Understand the Goal** - What are we building and why?
2. **Clarify Requirements** - Ask questions, get answers, incorporate into spec
3. **Brainstorm Approaches** - Explore options, user chooses one
4. **Architecture Discussion** - For larger tasks, discuss system design
5. **Testing Plan** - Define how to verify the implementation
6. **Write the Spec** - Create the markdown file in `specs/pending/`

## Process

### Step 1: Understand the Goal

First, understand what the user wants to accomplish. Ask:

- What is the main goal or feature being requested?
- What problem does this solve?
- Who is the intended user or stakeholder?
- What is the scope? (MVP vs full feature, one-time vs recurring)
- Are there any time constraints or deadlines?

**Example prompts:**
- "What's driving this feature request?"
- "Can you describe the user story from the end user's perspective?"
- "Is this addressing a specific pain point? What happens today without this?"

### Step 2: Clarify Requirements

Dig into specifics. Ask questions to fill gaps in the user's request. **Wait for answers before continuing.**

- What are the acceptance criteria?
- What inputs does the system need? What outputs should it produce?
- Are there edge cases to consider?
- What constraints exist? (performance, security, compatibility)
- What should happen when things go wrong? (error handling)

**Good clarifying questions:**
- "What happens if [X fails/doesn't exist/is invalid]?"
- "Should this work for [edge case]?"
- "Are there any performance requirements?"
- "What's the expected usage pattern? Once a day? Once per request?"
- "Should this be configurable? What options would users need?"

**Do NOT create the spec yet.** Keep asking until you have:
- Clear requirements with user-confirmed answers
- Known inputs/outputs
- Error handling approach
- Any constraints or preferences

**Record all answers** - you'll incorporate them into the spec, not leave them as questions.

### Step 3: Brainstorm Implementation Approaches

Explore different ways to implement the feature. For each approach:

- Describe the solution briefly
- Identify pros and cons
- Consider complexity and effort
- Note any dependencies or prerequisites

**Avoid jumping to the first solution.** Instead, present 2-3 options when appropriate:

```
Here are a few approaches we could take:

**Option A: [Approach name]**
- How it works: [brief description]
- Pros: [advantages]
- Cons: [disadvantages]
- Effort: [rough estimate]

**Option B: [Alternative approach]**
...
```

**Wait for user to choose an approach** (or confirm a single approach for simple tasks).

THEN proceed to write the spec with the chosen approach incorporated.

### Step 4: Architecture Discussion (For Large Tasks)

For larger features or epics, discuss the system design:

- How does this fit into the existing architecture?
- What components need to change?
- Are there new abstractions needed?
- How will data flow through the system?
- What are the dependencies between components?

**Architecture questions:**
- "How does this integrate with [existing system X]?"
- "Should this be a new module/service or extend an existing one?"
- "What's the data flow from input to output?"
- "Are there any shared state or coordination needs?"

Use diagrams or ASCII art if helpful for explaining the architecture.

### Step 5: Testing Plan

Define how to verify the implementation works correctly:

- **Unit tests**: What components need unit tests?
- **Integration tests**: How do components work together?
- **E2E tests**: What user flows should be tested end-to-end?
- **Edge cases**: What unusual inputs or states should be tested?

**Testing questions:**
- "What's the happy path success case?"
- "What failure modes should we handle?"
- "Are there any security cases to test?"
- "What's the minimum test coverage needed for confidence?"

### Step 6: Write the Spec

After gathering requirements, getting answers, and confirming the approach, create the spec file. **All questions should be answered and incorporated into the spec.**

## Spec Format

The spec must be compatible with ralph-loop.sh. Use this structure:

```markdown
# [Feature Name]

## Overview

[1-2 sentences describing what this feature does and why]

## Background

[Optional: Context about why this is needed, what problem it solves]

## Requirements

- [ ] [Requirement 1]
- [ ] [Requirement 2]
- [ ] [Requirement 3]

## Tasks

- [ ] **Task 1**: [Brief description]
- [ ] **Task 2**: [Brief description]
- [ ] **Task 3**: [Brief description]

**Note**: Each task must be atomic - see "CRITICAL: Task Atomicity" section below.

## Implementation Details

### Task 1: [Task name]

[Detailed implementation steps]

### Task 2: [Task name]

[Detailed implementation steps]

## Testing Plan

### Unit Tests

- [Test case 1]
- [Test case 2]

### Integration Tests

- [Test case 1]

### E2E Tests (if applicable)

- [Test case 1]

## Verification Checklist

Before signaling `TASK_COMPLETE`, verify:

- [ ] [Behavioral verification 1 - e.g., "Error message includes status code"]
- [ ] [Behavioral verification 2 - e.g., "Timeout preserves cause chain"]

**Note**: CI checks (build, lint, test) are run automatically by ralph-loop.sh. Do NOT include commands like `npm run build` or `npm test` here. Focus on behavioral/functional verification that a human would check.

## Rollback Plan

[How to revert if something goes wrong]

## Future Considerations (Optional)

[Optional: Enhancements that could be added later - NOT required for current implementation]

## Spec Readiness Checklist

Before running ralph-loop.sh, verify:

- [x] All requirements are clearly defined (no unanswered questions)
- [x] All tasks are actionable and appropriately sized (1-4 hours each)
- [x] **All tasks are atomic (each task leaves codebase in working state)**
- [x] Implementation details are specific enough to execute
- [x] Testing plan covers happy path and error cases
- [x] Verification steps are concrete and testable
- [x] Rollback plan exists

## References

- [Link 1]
- [Link 2]
```

## Task Granularity

Tasks should be sized appropriately for ralph-loop:

- **Good tasks**: A single developer could complete in 1-4 hours
- **Too large**: "Implement the entire feature" - break it down
- **Too small**: "Add a variable" - combine with related work

**Signs a task is too large:**
- Has multiple distinct phases
- Contains "and" or "then" in the description
- Requires multiple files in unrelated areas
- The developer would need to pause and plan mid-task

**Signs a task is too small:**
- Can be done in under30 minutes
- Doesn't warrant a separate commit
- Is a sub-step of a larger change

### CRITICAL: Task Atomicity

**Tasks MUST be atomic** - each task must leave the codebase in a working, compilable state. 

**Atomicity Rules:**

1. **Type signature changes include all usage sites**: When changing a function/class signature, ALL places that use it must be updated in the same task. Never split signature changes and their usage updates into separate tasks.

   ❌ **WRONG** (breaks build between tasks):
   ```
   Task 1: Update Error class signature to require fields
   Task 2: Update all usage sites to pass fields
   ```
   
   ✅ **CORRECT** (atomic):
   ```
   Task 1: Update Error class signature AND all usage sites (including tests)
   ```

2. **No intermediate broken states**: If completing Task 1 would break the build, typecheck, or tests, it's not a valid task. Merge it with Task 2.

3. **Each task should pass CI**: After completing any single task, running build/lint/test should succeed.

4. **Tests are part of the implementation task**: When possible, each task should include its own tests, not have a separate "Add tests" task at the end.

   ❌ **WRONG** (tests separated from implementation):
   ```
   Task 1: Add feature X
   Task 2: Add feature Y
   Task 3: Add unit tests for features X and Y
   ```
   
   ✅ **CORRECT** (tests co-located with implementation):
   ```
   Task 1: Add feature X (including tests)
   Task 2: Add feature Y (including tests)
   ```
   
   This ensures each task is independently verifiable and complete.

**How to identify atomicity violations:**

Ask yourself: "If ralph-loop completes Task 1 and stops, could Task 2 start successfully without Task 1 being broken?"
- If Task1 would cause type errors → Not atomic, merge tasks
- If Task 1 would cause lint errors → Not atomic, merge tasks  
- If Task 1 would cause test failures → Not atomic, merge tasks
- If Task2 depends on Task 1 being complete → OK, this is normal sequential dependency

**Examples:**

- **Changing a React prop type**: Update the component AND all parent components in one task, and include tests for the prop validation
- **Adding a required field to an error class**: Update the class definition AND every place that constructs the error (including tests) in one task
- **Renaming a function**: Update the function definition AND all call sites in one task, update tests in same task
- **Adding a new dependency**: Install it AND update any code using it in one task if it breaks existing code
- **Implementing a new feature**: Write the implementation AND its tests in the same task

**Note**: Normal sequential dependencies are fine (Task2 needs Task 1 complete to add new functionality). The issue is tasks that create broken intermediate states.

## Example Spec

Here's an example of a well-formed spec:

```markdown
# Add Rate Limiting to API Endpoints

## Overview

Implement rate limiting on all public API endpoints to prevent abuse and ensure fair usage. Rate limits will be configurable per endpoint and per user tier.

## Background

We've seen increased API usage that occasionally degrades performance for all users. Rate limiting will protect the system while allowing legitimate use patterns.

## Requirements

- [ ] Rate limit requests per minute per API key
- [ ] Different limits for free tier (60/min) vs paid tier (1000/min)
- [ ] Return 429 status with retry-after header when limit exceeded
- [ ] Log rate limit hits for monitoring

## Tasks

- [ ] **Task 1**: Create RateLimitService with configurable limits
- [ ] **Task 2**: Add rate limiting middleware to API routes
- [ ] **Task 3**: Update API documentation with rate limit info
- [ ] **Task 4**: Add monitoring dashboard for rate limit events

## Implementation Details

### Task 1: Create RateLimitService

Create `src/services/rate-limit.service.ts`:

1. Define `RateLimitConfig` type with `maxRequests` and `windowMs`
2. Create `RateLimitService` using Effect TS service pattern
3. Implement `checkLimit(key: string, config: RateLimitConfig)` method
4. Use RcMap for managing rate limit state per key
5. Store request counts in SimpleCache with TTL

### Task 2: Add Rate Limiting Middleware

Update API route handlers:

1. Import RateLimitService in affected routes
2. Add `rateLimitMiddleware` before request handlers
3. Configure tier-based limits from user context
4. Return 429 with proper headers on limit exceeded

...

## Testing Plan

### Unit Tests

- RateLimitService allows requests under limit
- RateLimitService blocks requests over limit
- RateLimitService resets after window expires
- Different configs for different tiers

### Integration Tests

- Full request flow with rate limiting
- Concurrent request handling
- Header validation on 429 responses

## Verification Checklist

Before signaling `TASK_COMPLETE`, verify:

- [ ] Rate limit enforcement works correctly (manual testing)
- [ ] Documentation updated with rate limit info
- [ ] No regressions in existing API responses

**Note**: CI checks (build, lint, test) are run automatically by ralph-loop.sh.

## Rollback Plan

1. Disable rate limiting via feature flag
2. Remove middleware from routes
3. Revert RateLimitService addition

## Future Considerations (Optional)

- Per-endpoint rate limits (currently global)
- Sliding window vs fixed window algorithm
- Redis-based distributed rate limiting for multi-instance deployments

## Spec Readiness Checklist

Before running ralph-loop.sh, verify:

- [x] All requirements are clearly defined (no unanswered questions)
- [x] All tasks are actionable and appropriately sized (1-4 hours each)
- [x] **All tasks are atomic (each task leaves codebase in working state)**
- [x] Implementation details are specific enough to execute
- [x] Testing plan covers happy path and error cases
- [x] Verification steps are concrete and testable
- [x] Rollback plan exists
```

## Writing Tips

### Be Specific
- Instead of "Add tests", specify what to test
- Instead of "Update documentation", specify which files and sections
- Include file paths, function names, and expected behaviors

### Include Context
- Explain why each task matters
- Reference existing code patterns to follow
- Note any dependencies between tasks

### Make Tasks Independent but Atomic
- Each task should be completable on its own without breaking the build
- Minimize cross-dependencies (but sequential dependencies are OK)
- Order tasks logically, but allow for reordering
- **See "CRITICAL: Task Atomicity" section above** - tasks must leave codebase working

### Verification Should Be Behavioral
- Focus on what to verify, not how to run CI (ralph-loop handles CI)
- Specify expected behavioral outcomes
- Include manual testing steps when needed
- DO NOT include commands like `npm run build`, `npm test`, `npm run lint` - ralph-loop runs these automatically

### No Unanswered Questions

**Critical**: The final spec should have NO unanswered questions in it.

- If you asked questions during planning, incorporate the answers into the spec
- If something is truly unknown AND critical, the spec is not ready - ask more questions
- "Future Considerations" can list optional enhancements, but core functionality must be defined
- A spec ready for ralph-loop means: a developer could pick it up and implement it without asking questions

## Output Location

Always write specs to: `specs/pending/[feature-name].md`

Use kebab-case for the filename: `add-rate-limiting.md`, `user-auth-flow.md`, `refactor-database-layer.md`

## After Creating the Spec

Once the spec is created:

1. Confirm the file was written correctly
2. Verify the "Spec Readiness Checklist" has all items checked
3. Remind the user they can run it with ralph-loop:
   ```
   ./ralph-loop.sh "[Feature name]"
   ```
4. Offer to iterate on the spec if the user has feedback

## Common Patterns

### Dependency Upgrade Spec
- List current and target versions
- Include breaking changes analysis
- Migration steps with rollback plan

### Bug Fix Spec
- Describe the bug clearly
- Root cause analysis (if known)
- Fix approach with test cases

### New Feature Spec
- User story from stakeholder perspective
- Acceptance criteria
- Architecture decisions
- Testing strategy

### Refactoring Spec
- Current state and problems
- Target state and benefits
- Migration plan with incremental steps
- How to verify behavior is preserved