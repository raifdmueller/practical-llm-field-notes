# What Doesn't Work Yet
> **⚠️ Note:** With MCP and tool integration, many 2023-2024 limitations are now solved. This section focuses on remaining challenges that better models or tools might address.

---

## Complex Multi-Step Reasoning
**Context:** Planning complex projects or architectures

**Experience:** Struggles with:
- Long-term project planning with multiple dependencies
- Complex business logic that requires deep domain understanding
- Multi-step mathematical proofs requiring sustained reasoning
- Strategic decision-making requiring extensive context synthesis
- Maintaining coherence across very long reasoning chains

**Learnings:** Break complex problems into smaller, manageable chunks with clear intermediate steps. Use iterative refinement rather than expecting complete solutions upfront.

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

## Deep Business Context Understanding
**Context:** Enterprise-specific solutions

**Experience:** Challenges include:
- Understanding complex organizational structures and politics
- Navigating company-specific processes and constraints
- Interpreting implicit business requirements
- Making decisions that require insider knowledge
- Understanding unstated cultural and contextual factors

**Learnings:** Provide extensive context documentation and use iterative refinement. Consider LLMs as external consultants who need thorough briefing.

## Long-term Learning & Adaptation
**Context:** Continuous improvement over time

**Experience:** Cannot:
- Learn from previous conversations or interactions
- Adapt behavior based on user preferences over time
- Build up domain expertise through repeated exposure
- Remember lessons learned from past mistakes
- Develop personalized approaches for specific users or teams

**Learnings:** Design workflows that capture and reuse important learnings explicitly. Use external systems to maintain context and preferences.

## Autonomous Agent Reliability
**Context:** Long-running autonomous workflows

**Experience:** Challenges with:
- Maintaining focus and direction over extended autonomous work
- Recovering gracefully from unexpected errors or edge cases
- Making reliable decisions without human oversight
- Coordinating complex multi-agent workflows
- Ensuring consistent quality over long autonomous sessions

**Learnings:** Use human-in-the-loop approaches for critical decisions. Design autonomous workflows with frequent checkpoints and validation steps.

## Cross-System Enterprise Integration
**Context:** Complex enterprise environments

**Experience:** Struggles with:
- Understanding intricate system interdependencies
- Navigating complex authentication and authorization schemes
- Handling enterprise-specific protocols and APIs
- Managing transaction consistency across multiple systems
- Understanding implicit system behaviors and side effects

**Learnings:** Start with simpler integrations and build complexity gradually. Maintain clear documentation of system interactions and dependencies.

---

## ✅ Now Solved with MCP/Tools (Moved from Limitations)

These were significant limitations but are now addressable:

### **Real-Time Data Integration** → MCP + web search/APIs
### **File System & Environment Interaction** → MCP + file system tools
### **Basic Data Analysis** → MCP + analysis tools
### **Simple Calculations** → MCP + code interpreters

---

## 🚀 Report New "Doesn't Work Yet" Areas

**Found limitations that might be solvable with better models or tools?**

We want to know about:
- **Reasoning limitations** that persist despite tools
- **Integration challenges** in complex environments  
- **Scalability issues** with current approaches
- **Reliability problems** in autonomous scenarios

**[Contribute Your Observations](CONTRIBUTING.md)**