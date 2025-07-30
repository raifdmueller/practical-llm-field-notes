# Workflows - Basic Patterns

> **💡 Motivation:** Document proven workflow patterns and integration approaches that enhance LLM effectiveness.
> 
> **📝 Content:** Workflow patterns, integration strategies, automation approaches - focused on HOW rather than specific tool names.

---

**📍 Navigation:** [Basic Patterns](05a-workflows-basic.md) | [Production Patterns](05b-workflows-production.md) | [Advanced Integration](05c-workflows-advanced.md)


## Helper Script & Automation Workflows

### Rapid Prototyping with Helper Scripts
**Approach:** Fast iteration from problem identification to working solution

**Workflow steps:**
1. **Problem identification** - Identify repetitive task or specific automation need
2. **Sample data collection** - Gather representative input examples
3. **Expected output definition** - Define exact success criteria
4. **LLM script generation** - Use [effective prompting techniques](04-tips-and-tricks.md#effective-helper-script-prompting)
5. **Testing and refinement** - Test with real data, iterate on edge cases
6. **Documentation and integration** - Add usage examples and integrate into workflow

**Example workflow:**
```
Problem: "Need to update all package.json files in a monorepo"
↓
Sample data: Collect 2-3 actual package.json files
↓  
Expected output: Define exactly what changes are needed
↓
LLM prompt: "Create a Node.js script that updates package.json files..."
↓
Test & refine: Run on sample files, handle edge cases
↓
Document: Add usage examples and error handling guide
```

**Best practices:**
- Start with smallest possible scope
- Always test with real data, not synthetic examples
- Build error handling incrementally
- Document the script's limitations clearly

**Time investment:** 15-30 minutes for simple scripts, 1-2 hours for complex ones

### Template-First Development
**Approach:** Standardize LLM outputs using well-designed templates

**Implementation strategy:**
1. **Template design** - Create structure with clear sections and examples
2. **Template validation** - Test with multiple use cases to ensure flexibility
3. **LLM integration** - Train prompts to use template consistently
4. **Quality assurance** - Establish review criteria for template adherence
5. **Template evolution** - Refine based on usage patterns and feedback

**Template development workflow:**
```
Identify repetitive content type (ADRs, user stories, etc.)
↓
Analyze 5-10 high-quality existing examples
↓
Extract common structure and required elements
↓
Create template with guidance and examples
↓
Test template with LLM using different scenarios
↓
Refine based on output quality and consistency
↓
Document template usage and customize for team
```

**Template categories that work well:**
- **Architecture Decision Records** - Structured decision documentation
- **User Story Creation** - Consistent requirement capture
- **Meeting Notes** - Standardized action item tracking
- **Code Review Templates** - Systematic review criteria
- **Project Specifications** - Repeatable project structure

**Success metrics:**
- Consistency of output format across team members
- Reduction in review cycles for template-based content
- Faster onboarding for new team members

### Example-Driven Iteration
**Approach:** Build complexity gradually using working examples as foundation

**Iteration pattern:**
1. **Baseline example** - Start with simple, working example
2. **Requirement analysis** - Compare target needs to baseline
3. **Incremental modification** - Modify one aspect at a time
4. **Validation** - Test each modification thoroughly
5. **Pattern extraction** - Document what makes modifications successful
6. **Generalization** - Create reusable patterns for similar modifications

**Progressive complexity example:**
```
Example 1: Simple API call script
↓
Example 2: Add error handling and retries  
↓
Example 3: Add authentication and rate limiting
↓
Example 4: Add configuration file support
↓
Example 5: Add logging and monitoring
↓
Final: Production-ready API client library
```

**Guidelines for effective iteration:**
- Change only one aspect per iteration
- Always maintain working state
- Document why each change was needed
- Test thoroughly at each step
- Create examples that others can follow

---

