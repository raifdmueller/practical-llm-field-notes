# Tips & Tricks

> **💡 Motivation:** Collect practical techniques that increase effectiveness when working with LLMs.
> 
> **📝 Content:** Prompt engineering, workflow optimizations, hidden features, workarounds.

## Effective Prompting Strategies

### Context-First Approach
```
BAD: "Write a function to sort data"
GOOD: "I'm building a Python web app that processes user uploads. 
Write a function that sorts a list of dictionaries by timestamp, 
handling missing dates gracefully."
```

### Provide Your Context (Not LLM Role)
```
BAD: "Act as a senior software architect and review this code"
GOOD: "I'm a senior software architect working with Java microservices. 
I need help reviewing this code for scalability and maintainability issues."
```

**Why:** Telling the LLM your background gives context about your expertise level. Assigning roles to the LLM can actually limit its solution space in exploratory conversations.

### Let the LLM Lead (Role Reversal)
```
"I have a performance problem with my web application. 
Ask me questions to understand the issue, then suggest solutions."

"I need to design a new feature but I'm not sure about the approach. 
Interview me about the requirements and help me think through the options."
```

**Why:** Leverages the LLM's broad knowledge to explore solution spaces you might not consider.

### Step-by-Step Breakdown
```
Instead of: "Create a complete authentication system"
Try: "Let's build authentication step by step:
1. First, create a user registration form
2. Then we'll add password hashing
3. Finally, implement login sessions"
```

## Helper Script & Automation Techniques

### Effective Helper Script Prompting
**Core principle:** Provide concrete examples of input and expected output

```markdown
TEMPLATE:
"Create a [language] script that [specific task].

Input example:
[Paste actual sample data]

Expected output:
[Show exactly what you want to see]

Requirements:
- [Specific constraint 1]
- [Specific constraint 2]
- Handle edge case: [specific scenario]"
```

**Real example:**
```
Create a Python script that processes translation YAML files.

Input example (translations.yml):
```yaml
en:
  welcome: "Welcome"
  goodbye: ""
de:
  welcome: "Willkommen"
  goodbye: ""
```

Expected output:
- Identify missing translations (where value is empty)
- Generate list of keys needing translation
- Preserve file structure and comments

Requirements:
- Use PyYAML library
- Handle nested keys
- Create backup before modification
```

**Success factors:**
- Always include real sample data (anonymized if needed)
- Show expected output format explicitly
- List edge cases and error conditions
- Specify libraries or constraints upfront

### Template Design Patterns
**Effective template structure:** 3-7 clear sections with specific expectations

```markdown
GOOD TEMPLATE STRUCTURE:
## [Section Name] (Required/Optional)
**Purpose:** [What this section achieves]
**Format:** [How to structure content]
**Example:** [Concrete example]

---

EXAMPLE: ADR Template
## Context (Required)
**Purpose:** Explain the problem and why a decision is needed
**Format:** 2-3 paragraphs describing current situation
**Example:** "We need to choose a database for our new microservice..."

## Decision (Required)  
**Purpose:** State the chosen solution clearly
**Format:** One clear sentence + brief justification
**Example:** "We will use PostgreSQL because..."
```

**Template design principles:**
- **Clear section purposes** - each section has a specific role
- **Format guidance** - tell the LLM how to structure content
- **Concrete examples** - show what good content looks like
- **Required vs Optional** - clarity on what must be included
- **Length guidance** - prevent over/under-detailed sections

### Example Selection Strategies
**Choose examples that maximize learning transfer**

**High-impact example characteristics:**
- **Complete and working** - no pseudo-code or incomplete snippets
- **Edge case handling** - shows error conditions and validation
- **Well-commented** - explains the reasoning behind decisions
- **Similar domain** - matches the target use case closely
- **Recent and relevant** - uses current libraries and patterns

**Example selection template:**
```
Here's a working example of [similar task]:

```[language]
[Complete, working code example]
```

Key aspects to adapt for your use case:
- [Aspect 1]: [How to modify]
- [Aspect 2]: [How to modify]
- [Aspect 3]: [How to modify]

Now create a similar solution for [your specific task].
```

**Multi-example strategy for complex tasks:**
```
I'll show you 3 examples of [pattern], then ask you to create one for [specific case]:

Example 1 (Simple case): [code]
Example 2 (With error handling): [code]  
Example 3 (Production ready): [code]

Now create a [pattern] for [your requirements], combining the best aspects of these examples.
```

## Context Management

### Structured Request Template
```
**Background:** [Your role/expertise level]
**Context:** [What you're building/working on]
**Goal:** [What you want to achieve]
**Constraints:** [Technical limitations, requirements]
**Format:** [How you want the output structured]
```

**Note:** Templates help you organize thoughts and provide complete context, but they're not magic formulas for better LLM performance.

### Iterative Refinement
- Start with a broad request to explore possibilities
- Ask for specific improvements based on initial output
- Build complexity gradually
- Save working versions before major changes
- Use "What would you change if..." for alternatives

## AI Agent Performance Optimization

### KV-Cache Optimization for Production Agents
*Source: [Context Engineering for AI Agents - Manus](https://manus.im/de/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)*

**Key insight:** KV-cache hit rate is the most critical metric for production AI agents, affecting both latency and costs.

**Cost impact:** Cached tokens cost 10x less than non-cached (e.g., Claude Sonnet: $0.30/MTok cached vs $3.00/MTok non-cached)

**Best practices:**
1. **Keep prompt prefix stable** - Even one token difference can invalidate the entire cache
   ```
   BAD: Including timestamps at start of system prompt
   GOOD: Stable system prompt, append timestamps at end if needed
   ```

2. **Make context append-only** - Never modify previous actions or observations
   ```
   BAD: Updating previous step results in-place
   GOOD: Always append new information to context
   ```

3. **Ensure deterministic serialization** - Many JSON libraries don't guarantee key order
   ```python
   # Good: Stable serialization
   json.dumps(data, sort_keys=True)
   ```

4. **Mark cache breakpoints explicitly** when using self-hosted models with frameworks like vLLM

### Tool Management for Agents

**Problem:** Complex agents with many tools become "dumber" due to action space explosion

**Solution:** Mask tools instead of removing them
- Maintains stable KV-cache (tool definitions stay in context)
- Prevents confusion from missing tool references
- Use prefix-based tool organization (e.g., `browser_`, `shell_`, `file_`)

**Implementation example:**
```
Auto mode: <|im_start|>assistant
Required mode: <|im_start|>assistant<tool_call>
Specific mode: <|im_start|>assistant<tool_call>{"name": "browser_
```

## Technical Considerations

### Specify Your Stack (Mind the Knowledge Cutoff)
```
GOOD: "Using React 18 with TypeScript, Tailwind CSS, and Vite..."
BETTER: "Using React 18 (released March 2022) with TypeScript..."
BEST: "Using React 18 - here's the current API documentation: [paste relevant docs]"
```

**Why:** LLMs have knowledge cutoffs. For newer technologies or versions, provide current documentation or use tools like GitMCP to extend knowledge.

### Request Concrete Examples
```
BAD: "Explain error handling best practices"
GOOD: "Show me 3 specific error handling patterns for REST APIs, 
with code examples for each"
```

### Ask for Alternatives and Trade-offs
```
"Show me 2-3 different approaches to solve this, with pros/cons for each"
"What are the trade-offs between approach A and B for my use case?"
```

## Recovery and Refinement

### When the LLM Doesn't Understand
```
"Let me rephrase that differently..."
"Here's a concrete example of what I mean: [example]"
"What additional information do you need to help me with this?"
```

### Course Correction
```
"That's close, but I need something more focused on [specific aspect]"
"The approach is right, but can you adapt it for [different constraint]?"
```

### Knowledge Gaps
```
"If you're not sure about [specific technology], let me know what 
information would help you give better advice"
```

## Advanced Techniques

### Constraint-Based Prompting
```
"I need a solution that works with these specific constraints:
- Must be under 100 lines of code
- Cannot use external libraries
- Must handle 1000+ concurrent users"
```

### Perspective Switching
```
"Now look at this same problem from a security perspective"
"What would a DevOps engineer be concerned about with this approach?"
```

### Error-First Thinking
```
"What could go wrong with this approach?"
"What edge cases should I be worried about?"
"How would this fail at scale?"
```

---

## 🚀 Share Your Techniques

**Have techniques that consistently work for you?**

We especially want:
- **Specific prompting patterns** with before/after examples
- **Context strategies** that improve response quality
- **Recovery techniques** for when things go wrong
- **Domain-specific tips** for your area of expertise

**[Contribute Your Tips](CONTRIBUTING.md)**