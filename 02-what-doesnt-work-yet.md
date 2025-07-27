# What Doesn't Work Yet

> **💡 Motivation:** Set realistic expectations and identify areas that might improve with future developments.
> 
> **📝 Content:** Limitations that are technology-related but potentially solvable. Distinguish between "not yet" and "fundamentally not possible."

## Complex Multi-Step Reasoning
**Context:** Planning complex projects or architectures

**Experience:** Struggles with:
- Long-term project planning with multiple dependencies
- Complex business logic that requires deep domain understanding
- Multi-step mathematical proofs or complex calculations
- Strategic decision-making requiring extensive context

**Learnings:** Break complex problems into smaller, manageable chunks with clear intermediate steps.

## Documentation Generation (Architectural)
**Context:** Creating comprehensive system documentation

**Experience:** Limited by:
- Only sees code snippets, not overall system architecture
- Cannot explain "why" decisions were made, only "what" code does
- Generated documentation becomes outdated as code evolves
- Missing business context and architectural rationale
- Cannot understand cross-system dependencies and interactions

**Learnings:** Use LLMs for ad-hoc code explanations instead of generating static documentation. Focus on explaining specific code sections when needed rather than creating comprehensive docs that will age poorly.

## Complex Code Analysis & Review
**Context:** Advanced code review requiring deep understanding

**Experience:** Struggles with:
- Subtle race conditions and concurrency issues
- Complex business logic validation
- Performance implications of architectural decisions
- Integration patterns across multiple systems
- Domain-specific security considerations beyond common patterns

**Learnings:** Use LLMs for surface-level review and obvious issues, but rely on human experts for complex architectural and business logic problems.

## Real-Time Data Integration
**Context:** Working with live, changing data

**Experience:** Limited by:
- No access to real-time APIs or databases
- Cannot verify current information accuracy
- Struggles with time-sensitive decision making
- Cannot maintain state between conversations

**Learnings:** Combine LLM capabilities with real-time data tools and APIs.

## Deep Business Context Understanding
**Context:** Enterprise-specific solutions

**Experience:** Challenges include:
- Understanding complex organizational structures
- Navigating company-specific processes and constraints
- Interpreting business requirements without extensive context
- Making decisions that require insider knowledge

**Learnings:** Provide extensive context documentation and use iterative refinement.

## File System & Environment Interaction
**Context:** Direct system administration and file manipulation

**Experience:** Cannot reliably:
- Perform complex file operations across directories
- Understand system state and environment variables
- Execute long-running scripts or processes
- Manage dependencies and environment setup

**Learnings:** Use LLMs for planning and code generation, then execute manually or through proper tooling.