# Workflows
This section has been split into three focused parts for better navigation:

## 📚 **[Basic Patterns](05a-workflows-basic.md)**
Essential workflows for getting started with LLM-assisted development:
- Helper Script & Automation Workflows
- Rapid Prototyping with Helper Scripts  
- Template-First Development
- Example-Driven Iteration

*Perfect for: Beginners, individual developers, simple automation tasks*

---

## 🏭 **[Production Patterns](05b-workflows-production.md)**
Battle-tested patterns for production environments:
- Context Optimization Pattern
- Context Handoff Pattern
- Progressive Disclosure Pattern
- Specification-First Pattern
- Tool-Specific Integration Workflows

*Perfect for: Teams, production systems, scalable workflows*

---

## 🚀 **[Advanced Integration](05c-workflows-advanced.md)**
Sophisticated patterns for complex environments:
- Multi-Model Orchestration
- Learning Loop Pattern
- Team Coordination Patterns
- Advanced AI Agent Workflows
- Enterprise Integration Patterns

*Perfect for: Large teams, enterprise environments, complex integrations*

## 🎯 Additional Team Patterns

### The "LLM Style Guide" Pattern
**Problem:** Different developers get different LLM suggestions for same problems  
**Solution:** Team-specific prompting guidelines and code style enforcement  

**Implementation:**
```markdown
# Team LLM Guidelines
## Code Style Preferences
- Use TypeScript strict mode
- Prefer functional components over class components
- Use Tailwind CSS for styling
- Follow SOLID principles

## Prompting Standards
- Always specify our tech stack in prompts
- Include performance and accessibility requirements
- Ask for test cases with implementations
- Request error handling for all external API calls
- Use domain-specific trigger terms (arc42, DDD, TDD) to activate pre-trained knowledge
- Specify deviations from standard patterns rather than explaining entire concepts
- Combine multiple frameworks (e.g., "Apply DDD within Clean Architecture using arc42 docs")

**See also:** [Domain-Specific Trigger Terms](04-tips-and-tricks.md#domain-specific-trigger-terms) for detailed implementation guidelines.

## Quality Gates
- All LLM-generated code requires peer review
- Security review for authentication/authorization code
- Performance review for data processing functions
```

**Results:** 85% consistency in LLM-generated code across team members

### The "Pair Programming with AI" Pattern
**Problem:** Solo development with LLMs misses collaboration benefits  
**Solution:** Structured human-AI collaborative development process  

**Implementation:**
```markdown
# Pair programming session structure
1. Human defines the problem and constraints
2. LLM suggests approach and implementation plan
3. Human reviews and provides feedback/modifications
4. LLM implements with human providing real-time guidance
5. Human refactors and optimizes the result
```

**Results:** 25% better code quality compared to solo LLM development

**Session Structure:**
- **Planning Phase** (5 min): Define problem and success criteria
- **Design Phase** (10 min): LLM proposes solution, human refines
- **Implementation Phase** (20 min): Collaborative coding
- **Review Phase** (5 min): Quality check and optimization

### The "Code Review Assistant" Pattern
**Problem:** LLM-generated code bypasses normal review processes  
**Solution:** Enhanced code review process specifically for LLM-assisted development  

**Implementation:**
```yaml
# Enhanced review checklist for LLM code
Standard Review:
  - Functionality and business logic
  - Code style and conventions
  - Performance considerations

LLM-Specific Review:
  - Are imports/dependencies real?
  - Does error handling make sense?
  - Are security practices properly implemented?
  - Is the approach over-engineered for the problem?
```

**Results:** 60% reduction in production bugs from LLM-generated code

---

**🔍 Quick Navigation:**
- **New to LLM workflows?** → Start with [Basic Patterns](05a-workflows-basic.md)
- **Building production systems?** → Jump to [Production Patterns](05b-workflows-production.md)  
- **Managing complex integrations?** → Explore [Advanced Integration](05c-workflows-advanced.md)

---

## 🚀 Share Your Workflow Patterns

**Help us grow this collection with YOUR proven workflow patterns!**

[Contribute Your Pattern](CONTRIBUTING.md) or [Discuss Your Experience](../../issues)
