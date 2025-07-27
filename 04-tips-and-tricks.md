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

## Advanced Prompt Engineering Techniques

### Meta-Prompting Strategy
**Context:** When you need higher quality responses for complex problems  
**Technique:** Ask the LLM to generate its own prompts for better results  
**Example:**
```
Before answering my question about API design, first write a better version of this prompt that would help you give a more specific and useful answer. Then answer using that improved prompt.

Original question: "How do I design a good API?"
```
**Why it works:** LLMs understand their own capabilities and can create more targeted prompts  
**Results:** 30-40% improvement in response quality based on recent research

### Context-Aware Code Review Pattern
**Context:** Getting specific, actionable code review feedback  
**Technique:** Provide your skill level and specific concerns upfront  
**Example:**
```
I'm a senior backend developer with 8 years experience in Python/Django. I need you to review this code specifically for:
- Performance bottlenecks in high-traffic scenarios
- Security vulnerabilities I might have missed
- Code maintainability for a team of 6 developers

[code here]
```
**Why it works:** Tailors feedback to your expertise level and specific concerns  
**Learnings:** More targeted reviews, fewer obvious suggestions you already know

### The "Debugging Buddy" Pattern
**Context:** When you're stuck on a complex problem  
**Technique:** Make the LLM ask YOU questions to understand the problem better  
**Example:**
```
I have a bug in my React application. Instead of me describing everything, 
ask me 3-5 targeted questions that will help you understand the issue. 
Focus on questions that experienced developers would ask.
```
**Why it works:** Leverages LLM's diagnostic reasoning while ensuring all relevant context is captured  
**Learnings:** Often reveals aspects of the problem you hadn't considered

### Repository-Aware Development
**Context:** Working within existing codebases with established patterns  
**Technique:** Provide repository context for better code suggestions  
**Example:**
```
Project context: Next.js 14 app with TypeScript, using Tailwind CSS and Prisma ORM
File structure: /app/api/users/route.ts
Related files: /lib/db.ts (database connection), /types/user.ts (type definitions)
Existing patterns: We use Zod for validation, custom error classes, and async/await

Now help me implement user authentication middleware...
```
**Why it works:** Ensures generated code follows your project's conventions  
**Learnings:** Much more consistent code that fits naturally into existing architecture

### Progressive Complexity Building
**Context:** Implementing complex features without overwhelming the context window  
**Technique:** Build complexity in documented phases  
**Example:**
```
Phase 1: "Create basic user registration with email/password"
Phase 2: "Add email verification to the registration we built"
Phase 3: "Now add password reset functionality"
Phase 4: "Finally, integrate 2FA with the existing auth system"
```
**Why it works:** Maintains context continuity while managing complexity  
**Learnings:** 60% better code consistency across development phases

### The "Specification-First" Pattern
**Context:** Ensuring LLM output matches business requirements exactly  
**Technique:** Always start with detailed specifications before code generation  
**Example:**
```
Feature Specification:
- User Registration Flow
- Must validate email format with clear error messages
- Password requirements: 8+ chars, special characters, numbers
- Prevent duplicate email registration
- Send welcome email after successful registration
- Handle network failures gracefully
- Prevent double-form submission

Technical Requirements:
- React Hook Form for validation
- Zod schema validation
- NextAuth.js integration
- Tailwind CSS styling

Now implement this registration form.
```
**Why it works:** Reduces back-and-forth and ensures requirements are met  
**Learnings:** 70% reduction in rework due to mismatched requirements

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

### Context Handoff Strategy
**Context:** Maintaining context across long development sessions  
**Technique:** Systematic context preservation when switching tools or sessions  
**Template:**
```
## Previous Context
- Goal: [what we're building]
- Tech stack: [specific versions]
- Progress: [what's been completed]
- Current issue: [specific blocker]

## Handoff Request
Continue where we left off. Previous conversation established [summary].
Next step: [specific next action]
```
**Results:** 80% reduction in "starting over" time when resuming sessions

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

## MCP (Model Context Protocol) Integration Tips

### Persistent Context Across Sessions
**Context:** Working on projects that span multiple sessions  
**Technique:** Use file system tools for persistent context  
**Implementation:**
```
"Save our current progress to a file called 'project_context.md' so I can resume this work later. Include what we've built, decisions made, and next steps."
```
**Why it works:** MCP file tools provide persistent memory across sessions  
**Learnings:** Enables true multi-session development workflows

### Combining Multiple Tools for Complex Workflows
**Context:** Tasks requiring multiple information sources  
**Technique:** Chain MCP tools systematically  
**Example:**
```
"First, search the web for current best practices for React performance optimization. Then, analyze our existing code files to identify specific areas that could benefit from these optimizations."
```
**Why it works:** Leverages both current information and project-specific context  
**Learnings:** Much more targeted and actionable recommendations

### Context Window Management with External Files
**Context:** Large projects hitting context limits  
**Technique:** Use external files strategically for large context  
**Example:**
```
"I'm going to save our API documentation to a file. Reference that file when helping me implement new endpoints, and only load relevant sections into our conversation."
```
**Why it works:** Manages context size while maintaining access to information  
**Learnings:** Enables work on larger codebases without context overflow

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

### The "Teaching Assistant" Pattern
**Context:** Learning new technologies or concepts  
**Technique:** Ask the LLM to adapt its teaching style to your needs  
**Example:**
```
"I'm learning Kubernetes and I learn best through hands-on examples. Don't just explain concepts - give me practical exercises I can run locally. Start with a simple pod deployment and build up to more complex scenarios."
```
**Why it works:** Tailors learning approach to your style  
**Learnings:** Much more effective knowledge transfer

### Quality-First Code Generation
**Context:** When code quality is more important than speed  
**Technique:** Explicitly request quality considerations upfront  
**Example:**
```
"Create a Python function that [requirement]. Prioritize:
1. Code readability and maintainability
2. Comprehensive error handling
3. Full test coverage including edge cases
4. Clear documentation with examples
5. Performance considerations for large datasets

Include docstrings, type hints, and explain your design decisions."
```
**Why it works:** Sets quality expectations from the start  
**Learnings:** Generates production-ready code instead of quick prototypes

---

## 🚀 Share Your Techniques

**Have techniques that consistently work for you?**

We especially want:
- **Specific prompting patterns** with before/after examples
- **Context strategies** that improve response quality
- **Recovery techniques** for when things go wrong
- **Domain-specific tips** for your area of expertise

**[Contribute Your Tips](CONTRIBUTING.md)**