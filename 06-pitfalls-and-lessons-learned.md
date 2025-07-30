# Pitfalls & Lessons Learned

> **💡 Motivation:** Document common mistakes and misconceptions to help others avoid similar problems.
> 
> **📝 Content:** Typical beginner mistakes, subtle issues, unexpected behaviors.

---

## Core Development Pitfalls

### The LLM as Scripting Engine Trap

**Problem:** Using LLMs directly as runtime scripting engines instead of code generators

**Context:** Building applications where business logic, data processing, or API integrations need to be flexible and user-configurable

**Experience:** The temptation is strong - let users describe what they want in natural language, send that to an LLM at runtime, and execute the result. This seems like the ultimate flexible system.

**What actually happens:**
- Same input produces different outputs on different runs
- Business logic becomes unpredictable and unreliable
- Debugging becomes impossible - no consistent execution path
- Production systems fail in subtle, hard-to-reproduce ways
- Performance suffers from LLM latency in critical paths
- Costs explode from repeated LLM calls
- Compliance and auditing become nightmares

**Real-world example:**
```javascript
// THE TRAP - DON'T DO THIS
async function processUserData(userRequest, data) {
    const result = await llm.query(`
        Process this data: ${JSON.stringify(data)}
        User wants: ${userRequest}
        Return the processed result
    `);
    return JSON.parse(result); // Non-deterministic disaster!
}
```

**Why it seems appealing:**
- Appears to offer ultimate flexibility
- Natural language configuration feels user-friendly
- Seems to eliminate complex rule engines
- Promises to handle edge cases automatically

**Why it fails:**
- LLMs are inherently non-deterministic
- Same prompt can yield different logic flows
- No way to test or validate business logic
- Error handling becomes impossible
- Performance is unpredictable

**Learning:** **Use LLMs as code generators, not code executors**

The correct approach:
```javascript
// THE SOLUTION - DO THIS INSTEAD
async function generateProcessor(userRequest) {
    const code = await llm.query(`
        Generate JavaScript function to: ${userRequest}
        Return only valid, testable JavaScript code
        Include error handling and validation
    `);
    
    // Validate, test, and compile the generated code
    const processor = validateAndCompile(code);
    return processor; // Now deterministic!
}

// Use the generated processor repeatedly
const processor = await generateProcessor("calculate tax based on region");
const result1 = processor(data1); // Consistent
const result2 = processor(data2); // Predictable
```

**Extended applications:**
- **Configuration management**: Generate config files, don't query for values
- **SQL queries**: Generate and validate queries, don't ask LLM to "process data"
- **API integrations**: Generate client code, don't route calls through LLM
- **Business rules**: Generate rule engines, don't evaluate rules via LLM
- **Data transformations**: Generate transformation functions, don't transform on-demand

**Warning signs you're in the trap:**
- Production logic depends on LLM responses
- Same input sometimes produces different business outcomes
- You can't write unit tests for core functionality
- Debugging requires analyzing LLM conversation logs
- Performance is inconsistent due to API latency

**The fundamental insight:** LLMs are excellent **development tools** but terrible **runtime engines**. Generate code during development or setup phases, then execute that code deterministically in production.

*Reference: [Scott Hanselman's LinkedIn post](https://www.linkedin.com/posts/shanselman_ai-ugcPost-7356096053965701121-3pR9)*

---

## AI Agent Development Pitfalls

*Source: [Context Engineering for AI Agents - Manus](https://manus.im/de/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)*

### The Few-Shot Pattern Trap

**Problem:** Agents get stuck in repetitive behavior when context contains similar action-observation patterns

**Context:** Building an agent to process multiple similar tasks (e.g., reviewing 20 resumes, processing documents)

**Experience:** 
- Agent starts performing well on first few items
- Gradually becomes mechanical, following exact same pattern
- Begins to miss unique aspects of each item
- May hallucinate expected responses instead of actual analysis
- Quality degrades without obvious warning signs

**Why it happens:**
- LLMs are excellent pattern mimics
- Uniform context creates strong behavioral imitation
- Agent follows established rhythm rather than adapting
- Few-shot examples become behavioral constraints

**Learning:**
- **Introduce controlled variation** in action formats and templates
- **Use different serialization approaches** for similar tasks
- **Vary prompts and response formats** to break patterns
- **Monitor for mechanical behavior** in repetitive workflows
- **Add deliberate "noise"** to prevent over-rigid patterns

**Warning signs:**
- Responses becoming increasingly similar
- Agent not adapting to unique aspects of tasks
- Declining attention to specific details

### The Clean-Slate Fallacy

**Problem:** Hiding failures from agents prevents learning and adaptation

**Context:** Multi-step agent workflows where errors occur

**Experience:**
- Agent makes mistake (hallucination, wrong tool call, etc.)
- Natural impulse: reset state, retry, or clean up the error
- Agent repeats same mistake because it has no evidence of failure
- Errors become systematic rather than occasional
- Debugging becomes impossible without failure context

**Why this happens:**
- Error traces provide crucial learning signals
- Context deletion removes adaptation opportunities
- Models can't improve without seeing consequences
- "Clean" contexts don't reflect real-world messiness

**Learning:**
- **Keep failure context** - let agents see their mistakes
- **Preserve error traces** in conversation history
- **Allow agents to learn** from their own corrections
- **Document failure patterns** for analysis
- **Trust the model's ability** to adapt from evidence

**Implementation:**
```
BAD:  [Error occurs] → Clear context → Retry
GOOD: [Error occurs] → Document failure → Continue with evidence
```

## Real Production Failures and Recovery Patterns

### Authentication and Authorization Failures

#### The Slack API Token Leak
**Problem:** Credential exposure through LLM interactions

**Context:** Developer debugging API integration issues with ChatGPT

**Experience:**
- Developer copied full curl command including live Slack API token
- Pasted command directly into ChatGPT prompt for debugging help
- Token was logged in conversation history, potentially accessible
- Token provided access to team communications and sensitive data

**Impact:** 
- Potential exposure of team communications
- Emergency token revocation and regeneration required
- Audit of all conversation logs for similar exposures

**Learning:**
- **Never paste live credentials** in LLM prompts
- Use placeholder values like `[API_KEY]` or `sk-xxx...xxx`
- Implement credential scanning in development workflows
- Train team on secure LLM interaction practices

**Prevention:**
```bash
# Good practice: Sanitize before sharing
curl -H "Authorization: Bearer [SLACK_TOKEN]" \
     -X POST https://slack.com/api/chat.postMessage \
     -d "channel=general&text=Hello"

# BAD: Live credentials exposed
curl -H "Authorization: Bearer xoxb-1234567890-abcdef..." \
     -X POST https://slack.com/api/chat.postMessage \
     -d "channel=general&text=Hello"
```

#### The "Helpful" Code Completion Security Flaw
**Problem:** LLM suggesting insecure patterns from training data

**Context:** Using GitHub Copilot for database connection code

**Experience:**
- Copilot suggested hardcoded database password in connection string
- Code looked professionally structured and complete
- Passed initial code review as "generated code"
- Near-miss caught during security review before production

**Impact:**
- Delayed release for security audit
- Required team training on reviewing AI-generated code
- Lost confidence in automated code generation

**Learning:**
- **LLM suggestions need security-focused review**, not just functional review
- Training data may include insecure historical patterns
- Treat generated code with same scrutiny as human-written code

**Prevention:**
```python
# What Copilot might suggest (BAD)
connection = psycopg2.connect(
    host="localhost",
    database="mydb",
    user="admin",
    password="admin123"  # Hardcoded password!
)

# Secure alternative
import os
connection = psycopg2.connect(
    host=os.environ.get('DB_HOST'),
    database=os.environ.get('DB_NAME'),
    user=os.environ.get('DB_USER'),
    password=os.environ.get('DB_PASSWORD')
)
```

### Context and Memory Failures

#### The Conversation Collapse
**Problem:** Long architectural discussions losing track of original requirements

**Context:** 3-hour Claude session designing microservices architecture

**Experience:**
- Started with clear requirements: "Design a scalable e-commerce platform"
- Added features incrementally over 50+ message exchanges
- By message 60, Claude suggested solutions incompatible with original constraints
- Final architecture violated initial performance and budget requirements

**Impact:**
- 3 hours of development work in wrong direction
- Team confusion about approved architecture
- Lost trust in LLM for complex planning

**Learning:**
- **Summarize and restate key constraints** periodically in long conversations
- Document architectural decisions as you make them
- Use checkpoints every 10-15 exchanges to verify alignment

**Prevention:**
```markdown
## Conversation Checkpoint (Message 25)
Let me summarize what we've established so far:
- E-commerce platform for 10K concurrent users
- Budget: $5K/month infrastructure cost
- Tech stack: Node.js, PostgreSQL, Docker
- Must handle 1M products, 100K orders/day

Are we still aligned on these constraints for the next phase?
```

#### The Version Drift Problem
**Problem:** Inconsistent code patterns across development sessions

**Context:** Team using ChatGPT to generate React components over several weeks

**Experience:**
- Week 1: Generated components using class-based React patterns
- Week 2: Switched to functional components with hooks
- Week 3: Mixed patterns within same components
- Resulted in inconsistent codebase difficult to maintain

**Impact:**
- Code review overhead increased 300%
- Junior developers confused by mixed patterns
- Refactoring required to standardize approach

**Learning:**
- **Establish and document coding patterns** before using LLM assistance
- Maintain style guides and reference them in prompts
- Use consistent prompt templates for similar tasks

**Prevention:**
```javascript
// Prompt template for consistency
"Generate a React functional component using our team standards:
- TypeScript with strict mode
- React hooks (useState, useEffect)
- Tailwind CSS for styling
- Props interface defined with TypeScript
- JSDoc comments for complex logic

Component requirement: [describe functionality]"
```

### Over-Reliance Incidents

#### The Confident Wrong Import
**Problem:** LLM suggesting non-existent APIs with high confidence

**Context:** Building React app with authentication features

**Experience:**
- GPT-4 confidently suggested `import { useAuthState } from 'react-firebase-hooks/auth'`
- Provided detailed usage examples and documentation
- Function name looked legitimate and followed naming conventions
- Actual export was different, causing build failures

**Impact:**
- 2 hours debugging why imports failed
- Lost confidence in LLM API suggestions
- Required manual verification of all suggested imports

**Learning:**
- **Always verify imports and API suggestions**, especially for newer libraries
- LLMs may confidently generate plausible-looking but incorrect APIs
- Quick compilation checks catch these errors early

**Prevention:**
```bash
# Quick verification workflow
npm install [package-name]
node -e "console.log(Object.keys(require('[package-name]')))"

# Or check package documentation directly
npm docs [package-name]
```

#### The Database Migration Disaster (Avoided)
**Problem:** LLM generating destructive operations without warnings

**Context:** Django project needing database schema changes

**Experience:**
- Claude generated migration that would drop existing data
- Migration looked professional with proper Django syntax
- Developer almost ran `python manage.py migrate` without review
- Manual review caught potential data loss before execution

**Impact:**
- Near-miss data loss incident
- Required new review process for LLM-generated migrations
- Team training on destructive operation risks

**Learning:**
- **Never run destructive operations** suggested by LLMs without thorough testing
- LLMs may not fully understand data preservation requirements
- Always test migrations on staging with production data copy

**Prevention:**
```python
# Always include data preservation checks
# migrations/0005_user_profile_changes.py

from django.db import migrations
from django.core.management import call_command

def preserve_user_data(apps, schema_editor):
    # Backup existing data before schema changes
    call_command('dumpdata', 'users', output='backup_users.json')

def reverse_preserve_user_data(apps, schema_editor):
    # Restore data if migration is reversed
    call_command('loaddata', 'backup_users.json')

class Migration(migrations.Migration):
    operations = [
        migrations.RunPython(preserve_user_data, reverse_preserve_user_data),
        # ... actual schema changes
    ]
```

### Hallucination Incidents

#### The Non-Existent Library Recommendation
**Problem:** LLM fabricating libraries with detailed documentation

**Context:** Looking for React form validation solution

**Experience:**
- ChatGPT recommended `react-advanced-forms` library
- Provided detailed usage examples, API documentation
- Described features that sounded exactly like project needs
- Library doesn't exist - examples were completely fabricated

**Impact:**
- 2 hours investigating why `npm install` failed
- Confusion about what libraries actually exist
- Developed distrust of LLM library recommendations

**Learning:**
- **Verify library existence** before implementing suggestions
- LLMs can generate convincing but fictional documentation
- Cross-reference with npm/package manager before starting

**Prevention:**
```bash
# Verification workflow
npm search [library-name]
npm view [library-name] --json
# Check GitHub/documentation exists
# Verify recent maintenance and activity
```

#### The Fake Performance Optimization
**Problem:** LLM suggesting non-existent React API features

**Context:** Optimizing React component performance

**Experience:**
- Claude suggested using `React.memo` with custom comparison function API
- Provided syntax that looked like valid React patterns
- Comparison function signature was completely fictional
- Runtime errors when attempting to use suggested API

**Impact:**
- Debugging session trying to understand runtime errors
- Confusion about React.memo actual capabilities
- Required reading official documentation to clarify

**Learning:**
- **Cross-reference API documentation** for optimization suggestions
- LLMs may combine real concepts in invalid ways
- Verify complex API usage with official sources

**Prevention:**
```javascript
// Always verify complex API usage
// Check React documentation for React.memo signature
const MemoizedComponent = React.memo(MyComponent, (prevProps, nextProps) => {
  // Return true if props are equal (skip re-render)
  // Return false if props are different (re-render)
  return prevProps.id === nextProps.id;
});

// Not: React.memo(MyComponent, { compare: customFunction }) // This doesn't exist!
```

### Production Monitoring Misses

#### The Silent Quality Degradation
**Problem:** LLM application quality declining without detection

**Context:** Content generation system for marketing team

**Experience:**
- Deployed LLM-powered blog post generation without quality monitoring
- Output quality gradually decreased over 2 weeks
- Content became repetitive and less engaging
- Discovered only through customer complaints about "weird" content

**Impact:**
- Customer complaints about content quality
- Emergency content audit and manual review required
- Lost confidence in automated content generation

**Learning:**
- **LLM applications need continuous quality monitoring**, not just uptime monitoring
- Quality degradation can be gradual and hard to detect
- Monitor both technical metrics and output quality

**Prevention:**
```python
# Quality monitoring framework
class ContentQualityMonitor:
    def __init__(self):
        self.quality_metrics = {
            'readability_score': 0.0,
            'originality_score': 0.0,
            'engagement_prediction': 0.0
        }
    
    def monitor_output(self, generated_content):
        # Calculate quality metrics
        readability = self.calculate_readability(generated_content)
        originality = self.check_originality(generated_content)
        
        # Alert if quality drops below threshold
        if readability < 0.7 or originality < 0.8:
            self.send_quality_alert(generated_content, readability, originality)
        
        # Store metrics for trend analysis
        self.store_quality_metrics(readability, originality)
```

#### The Cost Explosion
**Problem:** RAG system with poor context management leading to cost explosion

**Context:** Customer support chatbot with knowledge base integration

**Experience:**
- RAG system initially worked well with 1K tokens per query
- Poor context management caused token usage to grow gradually
- Average query size increased from 1K to 15K tokens over 1 month
- Monthly API costs exploded from $500 to $7,500

**Impact:**
- 15x increase in operational costs
- Emergency budget review and system redesign
- Management questioning ROI of LLM implementation

**Learning:**
- **Monitor token usage patterns**, not just request counts
- Context management directly impacts costs
- Implement token usage alerts and budgets per endpoint

**Prevention:**
```python
# Token usage monitoring
class TokenUsageMonitor:
    def __init__(self, monthly_budget=1000):
        self.monthly_budget = monthly_budget
        self.current_usage = 0
        self.usage_by_endpoint = {}
    
    def track_usage(self, endpoint, input_tokens, output_tokens):
        total_tokens = input_tokens + output_tokens
        self.current_usage += total_tokens
        
        # Track per endpoint
        if endpoint not in self.usage_by_endpoint:
            self.usage_by_endpoint[endpoint] = 0
        self.usage_by_endpoint[endpoint] += total_tokens
        
        # Alert if approaching budget
        usage_percentage = (self.current_usage / self.monthly_budget) * 100
        if usage_percentage > 80:
            self.send_budget_alert(usage_percentage)
        
        # Alert on unusual spike
        if total_tokens > 10000:  # Unusually large query
            self.send_spike_alert(endpoint, total_tokens)
```

### Integration and Workflow Failures

#### The Docker Environment Mismatch
**Problem:** LLM-generated configuration not matching target environment

**Context:** Setting up development environment for new team member

**Experience:**
- Asked LLM to generate Docker configuration for Python/Django app
- Generated config used Python 3.11 (latest at time of generation)
- Production environment was locked to Python 3.9 for compatibility
- "Works on my machine" problems when deploying

**Impact:**
- New developer spent day debugging environment issues
- Deployment pipeline failures in staging
- Required manual environment specification

**Learning:**
- **Specify target environment constraints** in prompts
- LLMs may use latest versions without considering compatibility
- Include environment specifications in all infrastructure requests

**Prevention:**
```dockerfile
# Specify exact versions in prompts
"Generate a Dockerfile for Python Django app with these requirements:
- Python 3.9 (exact version for production compatibility)
- Node.js 16.x for frontend build
- PostgreSQL 13 client libraries
- Alpine Linux base for smaller image size"

# Result: Dockerfile with pinned versions
FROM python:3.9-alpine
# ... rest of configuration
```

#### The API Rate Limit Cascade
**Problem:** LLM-generated code not handling rate limits properly

**Context:** Building social media aggregation service

**Experience:**
- LLM generated API client code without rate limiting
- Code worked fine during development with low usage
- Production load triggered API rate limits
- Client retried immediately causing request queue explosion

**Impact:**
- Service degradation during peak hours
- Potential API account suspension threats
- Emergency hotfix deployment required

**Learning:**
- **LLMs may not suggest proper rate limiting patterns**
- Always include error handling and retry logic requirements
- Test with realistic load before production deployment

**Prevention:**
```python
# Include rate limiting in prompt
"Generate API client code with these requirements:
- Exponential backoff retry strategy
- Rate limiting with 100 requests per minute
- Circuit breaker pattern for API failures
- Proper error handling for 429 status codes"

# Better generated code
import time
from functools import wraps

def rate_limited(max_per_minute=100):
    def decorator(func):
        calls = []
        @wraps(func)
        def wrapper(*args, **kwargs):
            now = time.time()
            # Remove calls older than 1 minute
            calls[:] = [call for call in calls if call > now - 60]
            
            if len(calls) >= max_per_minute:
                sleep_time = 60 - (now - calls[0])
                time.sleep(sleep_time)
            
            calls.append(now)
            return func(*args, **kwargs)
        return wrapper
    return decorator
```

### Process and Team Coordination Failures

#### The Divergent Development Paths
**Problem:** Team members using different LLMs leading to inconsistent approaches

**Context:** Three developers implementing similar features simultaneously

**Experience:**
- Developer A used ChatGPT, suggested Redux for state management
- Developer B used Claude, recommended Zustand for state management  
- Developer C used GitHub Copilot, generated vanilla React state
- Integration became nightmare with three different patterns

**Impact:**
- Integration delays as patterns didn't work together
- Code review confusion about which approach to follow
- Refactoring required to standardize on single approach

**Learning:**
- **Standardize LLM tools and prompting strategies** across team
- Document architectural decisions and reference them in prompts
- Use code review to catch inconsistent patterns early

**Prevention:**
```markdown
# Team LLM Guidelines Document
## Approved Tools
- Primary: Claude for architecture decisions
- Secondary: GitHub Copilot for code completion
- Avoid: ChatGPT for architecture (use only for research)

## Standard Prompts
- State Management: "Use Zustand for client state, React Query for server state"
- Styling: "Use Tailwind CSS with our custom design system tokens"
- Testing: "Generate Jest tests with React Testing Library"

## Architecture Decisions
- Refer to ADR-001 for state management decisions
- All LLM-generated code must follow ESLint configuration
```

#### The Documentation Drift
**Problem:** LLM-generated documentation becoming outdated

**Context:** API documentation generated with GPT-4

**Experience:**
- Generated comprehensive API documentation from code
- Documentation looked professional and complete
- Implementation changed but documentation wasn't updated
- Other teams integrated based on outdated documentation

**Impact:**
- Integration errors by other teams
- Lost development time debugging API mismatches
- Reduced trust in documentation accuracy

**Learning:**
- **Generated documentation needs same maintenance** as hand-written docs
- Automate documentation updates when code changes
- Include documentation updates in definition of done

**Prevention:**
```bash
# Automated documentation pipeline
#!/bin/bash
# pre-commit hook

# Check if API routes changed
if git diff --cached --name-only | grep -q "routes/"; then
    echo "API routes changed, updating documentation..."
    
    # Regenerate API documentation
    npm run generate-api-docs
    
    # Add updated docs to commit
    git add docs/api/
    
    echo "Documentation updated automatically"
fi
```

## Recovery Patterns That Work

### Quick Assessment Framework
When LLM-related issues occur in production:

1. **Scope the damage**
   - What systems/users are affected?
   - Is this a quality issue or system failure?
   - Are there security implications?

2. **Check fundamentals**
   - Are credentials/secrets exposed?
   - Is sensitive data involved?
   - Are there compliance concerns?

3. **Rollback options**
   - Can we revert to previous working state?
   - Is there a manual fallback process?
   - What's the rollback impact?

4. **Communication plan**
   - Who needs immediate notification?
   - What's the customer communication strategy?
   - How do we prevent similar issues?

### Prevention Strategies

#### "Trust but Verify" Approach
- Use LLM suggestions as starting points, not final answers
- Implement systematic verification for all generated content
- Maintain human oversight for critical decisions

#### Incremental Deployment
- Test LLM-generated changes in staging environments first
- Use feature flags for gradual LLM feature rollouts
- Monitor quality metrics during deployment phases

#### Peer Review Requirements
- Never deploy LLM-generated code without human review
- Implement LLM-specific code review checklists
- Train reviewers on common LLM failure patterns

#### Monitoring from Day One
- Set up quality and cost monitoring before production deployment
- Implement alerting for unusual patterns or degradation
- Track both technical metrics and business outcomes

## 🚀 Call for Contributions

**This section needs YOUR hard-won lessons!**

The most valuable insights come from real mistakes and unexpected discoveries. Share your experiences to help others avoid the same pitfalls.

### 🎯 What We're Looking For

**Real experiences with:**
- **Over-reliance failures** - When trusting LLM output too much backfired
- **Quality control disasters** - How hallucinations or errors slipped through
- **Workflow mistakes** - Process decisions that seemed good but caused problems
- **Context management fails** - When conversations went off the rails
- **Integration problems** - Tool or workflow combinations that didn't work
- **Security or compliance issues** - Where LLM usage created unexpected risks

### 📝 Contribution Template

```markdown
### [Descriptive Title of the Pitfall]

**Problem:** [Brief description of what went wrong]

**Context:** [What you were trying to achieve, environment, constraints]

**Experience:** [What actually happened - the failure or issue]
- [Specific details of the problem]
- [What made it worse or hard to detect]
- [Impact or consequences]

**Learning:** [How to avoid this pitfall]
- [Warning signs to watch for]
- [Better approaches or workflows]
- [Preventive measures]
```

---

*Your mistakes help others succeed! Share your lessons learned.*

**[Contribute via Pull Request](CONTRIBUTING.md) or [Share Your Story](../../issues)**