Create and manage a Finite State Machine workflow with spawned sub-agents: $ARGUMENTS

**CRITICAL ARCHITECTURE - SUB-AGENT ORCHESTRATION:**

## 1. ORCHESTRATOR RESPONSIBILITIES
The main agent acts ONLY as an FSM orchestrator and MUST:
- Parse FSM definition and generate state graph
- Display visual state diagram in EVERY response
- Spawn sub-agents for state execution using Task tool
- Handle state transitions based on sub-agent results
- Manage context passing between states
- Persist state to .claude/fsm/active_fsm.json
- NEVER execute state logic directly

## 2. VISUAL STATE DISPLAY (MANDATORY IN EVERY RESPONSE)
```
FSM: [Workflow Name]
═══════════════════

┌─────────────────────┐
│ ✓ STATE_NAME        │  (completed by sub-agent)
└─────────────────────┘
         ↓
┌═════════════════════┐
║ ▶ CURRENT_STATE     ║  (sub-agent: active ⏳)
└═════════════════════┘
         ↓
┌─────────────────────┐
│   PENDING_STATE     │  (pending)
└─────────────────────┘
```

State indicators:
- ✓ = Completed state
- ▶ = Current state (with sub-agent status)
- ⏳ = Sub-agent actively executing
- ✗ = Failed state

## 3. FSM DEFINITION SCHEMA
FSM definitions must follow this structure:
```yaml
name: "Workflow Name"
goal: "High-level workflow objective"
initial_state: "STATE_ID"
states:
  STATE_ID:
    prompt: "Specific task for sub-agent to execute"
    tools: ["list", "of", "allowed", "tools"]  # optional
    success_criteria: "Clear success conditions"
    transitions:
      success: "NEXT_STATE_ID"
      failure: "ERROR_STATE_ID"  # optional
      loop: "PREVIOUS_STATE_ID"  # optional
    timeout: 300  # seconds, default 300
    retry_count: 1  # default 0
```

## 4. SUB-AGENT EXECUTION PROTOCOL
For each state execution:
```
1. Construct sub-agent prompt:
   """
   FSM State: [STATE_NAME]
   Workflow Goal: [FSM_GOAL]
   Current Context: [NAMESPACED_CONTEXT]
   
   Task: [STATE_PROMPT]
   Success Criteria: [SUCCESS_CRITERIA]
   Allowed Tools: [TOOLS_LIST]
   
   Return structured result:
   {
     "status": "success|failure|loop",
     "context_update": { ... },
     "message": "Human-readable summary"
   }
   """

2. Spawn sub-agent:
   Task(
     subagent_type="general-purpose",
     description="FSM State: [STATE_NAME]",
     prompt=[constructed prompt above]
   )

3. Process result:
   - Validate JSON structure
   - Update namespaced context
   - Determine next state transition
   - Update visual diagram
```

## 5. CONTEXT MANAGEMENT
- Main context persisted across states
- Sub-agent updates namespaced by state:
  ```json
  {
    "workflow": { "goal": "...", "started": "..." },
    "state_name_1": { "output": "..." },
    "state_name_2": { "generated_file": "..." }
  }
  ```
- Prevents state collision and maintains clarity

## 5A. AUTONOMOUS TRANSITION LOGIC
The FSM MUST handle all transitions automatically:
```
Sub-agent returns "success" → Transition to states.transitions.success
Sub-agent returns "failure" → Transition to states.transitions.failure (or terminate)
Sub-agent returns "loop" → Transition to states.transitions.loop
Sub-agent timeout → Transition to states.transitions.failure (or terminate)
Sub-agent error → Transition to states.transitions.failure (or terminate)
No transition defined → Terminate workflow with clear error message
```
**NEVER ASK USER** - All transitions are deterministic based on FSM definition

## 6. ERROR HANDLING (FULLY AUTONOMOUS)
- **Malformed Response**: Auto-transition to failure state if defined, else terminate
- **Timeout**: Auto-transition after timeout (default 5 min) to failure state
- **Retry Logic**: Auto-retry up to retry_count times, then auto-transition
- **Failure Recovery**: ALWAYS auto-transition to defined failure state (NO user prompts)
- **Sub-agent Crash**: Log error and auto-transition to failure state or terminate
- **Missing Transition**: If no failure state defined, terminate workflow with error
- **NO USER INTERVENTION**: Workflow runs to completion without asking for decisions

## 7. WORKFLOW EXECUTION (AUTONOMOUS)
1. Parse and validate FSM definition
2. Initialize workflow in active_fsm.json
3. Display initial state diagram
4. Execute states autonomously:
   - Show diagram with sub-agent indicator
   - Spawn sub-agent with constructed prompt
   - Wait for result (with timeout)
   - Process result and update context
   - AUTO-TRANSITION based on result status
   - Update and display new diagram
   - Continue until terminal state reached
5. On completion, aggregate and display final results
6. **NEVER ASK USER**: All decisions made by FSM definition

## 8. LOOP HANDLING (AUTONOMOUS)
- Track loop iterations per cycle automatically
- Display count in visualization: "(loop: 3/10)"
- Maximum 10 iterations per loop
- Auto-exit to failure state after max (no user prompt)
- If no failure state, terminate workflow with loop limit error

## 9. QUICK EXAMPLE
```yaml
name: "Bug Fix Workflow"
goal: "Fix the authentication bug in login system"
initial_state: "analyze"
states:
  analyze:
    prompt: "Analyze the authentication bug: users can't login"
    success_criteria: "Root cause identified"
    transitions:
      success: "implement"
  implement:
    prompt: "Fix the identified authentication issue"
    success_criteria: "Code changes complete"
    transitions:
      success: "test"
  test:
    prompt: "Test the authentication fix"
    success_criteria: "All tests pass"
    transitions:
      success: "done"
      failure: "implement"  # loop back
```

## ENFORCEMENT
- Orchestrator NEVER executes state logic
- All state work MUST go through sub-agents
- Maintain visual feedback throughout
- Validate all sub-agent responses
- Clear separation of orchestration vs execution
- **100% AUTONOMOUS**: Never ask user for transition decisions
- **AUTO-TRANSITION**: Always follow FSM definition transitions
- **NO INTERRUPTIONS**: Run to completion without user input