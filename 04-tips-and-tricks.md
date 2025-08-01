# Tips & Tricks
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

---

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
## Domain-Specific Trigger Terms

### Leverage Pre-Trained Knowledge Patterns
**Context:** LLMs recognize established methodologies and can activate comprehensive knowledge without lengthy explanations  
**Technique:** Use domain-specific terms as "triggers" to activate relevant knowledge clusters in the LLM's latent space  

**Core Principle:** Instead of explaining concepts from scratch, reference established frameworks and specify only deviations.

### Effective Trigger Categories

**Software Architecture & Requirements:**
- `arc42` → Activates 12-chapter architecture template with quality attributes
- `EARS` (Easy Approach to Requirements Syntax) → Triggers "When [trigger] [system] shall [behavior]" patterns  
- `C4 Model` → Activates Context, Container, Component, Code diagramming
- `ADR` (Architecture Decision Records) → Triggers Nygard's decision documentation template

**Testing Methodologies:**
- `London School TDD` → Activates mock-heavy, interaction-based testing
- `Chicago School TDD` → Triggers state-based testing with real objects
- `BDD` (Behavior Driven Development) → Activates Given-When-Then scenario patterns
- `Property-Based Testing` → Triggers QuickCheck-style random input generation

**Development Practices:**
- `Domain-Driven Design (DDD)` → Activates bounded contexts, aggregates, entities
- `SOLID Principles` → Triggers Single Responsibility, Open/Closed, Liskov Substitution patterns
- `Clean Architecture` → Activates Uncle Bob's layered architecture with dependency inversion
- `Event Sourcing` → Triggers event-driven state management patterns

### Implementation Patterns

**Pattern 1: Direct Activation**
```
❌ Verbose: "I need help with documenting software architecture. Please create a structure that includes system context, building blocks, runtime scenarios..."

✅ Trigger: "Generate an arc42 architecture document for a microservices e-commerce platform. Focus on chapters 1-3 and 8."
```

**Pattern 2: Deviation Specification**  
```
❌ Verbose: "I want to do test-driven development where I write tests first, but I prefer using real objects instead of mocks..."

✅ Trigger: "Apply Chicago School TDD for this payment service. Deviate by including integration tests in the red-green-refactor cycle."
```

**Pattern 3: Framework Combination**
```
✅ Combined: "Apply DDD bounded contexts within Clean Architecture. Document decisions using ADRs following arc42 template."
```

### Guidelines for Effective Usage

**✅ Use Trigger Terms When:**
- Established methodologies with clear, consistent definitions
- Industry-standard frameworks extensively documented  
- Well-known acronyms with consistent interpretations
- Concepts likely included in LLM training data

**⚠️ Provide Context Instead When:**
- Company-specific internal frameworks
- Recently developed methodologies (post-training cutoff)
- Ambiguous terms with multiple interpretations
- Regional variations of established concepts

### Measuring Effectiveness

**Token Efficiency Gains:**
- 50-80% reduction in prompt length for established concepts
- More context window space for specific requirements
- Lower API costs through reduced token consumption

**Quality Improvements:**
- Deeper, more comprehensive knowledge activation
- Better alignment with established best practices
- More consistent responses across similar prompts

**Quick Test:** Compare LLM responses using trigger terms vs. detailed explanations. Trigger terms should produce more focused, comprehensive, and contextually appropriate responses.

Team workflow integration patterns help scale these approaches across larger development teams.

---

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