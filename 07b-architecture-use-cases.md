# Use Cases - Software Architecture
---

**📍 Navigation:** [Development](07a-development-use-cases.md) | [Architecture](07b-architecture-use-cases.md) | [Enterprise & Performance](07c-enterprise-use-cases.md)
## Real Architecture Success Stories

### Legacy System Documentation with Claude
**Context:** 15-year-old Java monolith, 500K+ lines, original team gone  
**Problem:** New team needs to understand system architecture for microservices migration  
**LLM Application:** Used Claude to analyze code chunks and generate architectural documentation  

**Implementation:**
```bash
# Systematic approach
1. Extract all @Service and @Controller classes
2. Feed to Claude in chunks of 50 classes max
3. Ask for component relationship diagrams
4. Generate PlantUML diagrams for each domain

# Automated documentation generation
#!/bin/bash
find src/ -name "*.java" | while read file; do
    echo "Documenting $file..."
    
    # Extract class and method signatures
    code_content=$(cat "$file")
    
    # Generate documentation with Claude
    claude_prompt="
    Analyze this Java class and provide:
    1. Component purpose and responsibilities
    2. Dependencies and relationships
    3. Business domain mapping
    4. Integration patterns used
    5. Potential microservice boundaries
    
    Class: $code_content
    
    Focus on architectural insights for system decomposition.
    "
    
    # Call Claude API and save analysis
    curl -X POST "https://api.anthropic.com/v1/messages" \
         -H "x-api-key: $CLAUDE_API_KEY" \
         -H "content-type: application/json" \
         -d "{\"model\": \"claude-3-sonnet-20240229\", \"max_tokens\": 2000, \"messages\": [{\"role\": \"user\", \"content\": \"$claude_prompt\"}]}" \
         | jq -r '.content[0].text' > "analysis/$(basename "$file" .java).md"
done
```

**Results:**
- **Time saved:** 6 weeks → 2 weeks for system understanding
- **Quality:** 90% accuracy in identifying service boundaries
- **Business impact:** Migration started 4 weeks earlier than planned
- **Documentation coverage:** 0% → 85% architectural knowledge captured

### API Design Review Automation
**Context:** Microservices team, 8 developers, REST API design reviews  
**Problem:** Inconsistent API designs, lengthy review cycles  
**LLM Application:** ChatGPT-4 for automated API design review  

**Implementation:**
```yaml
# Automated API review checklist
api_review_prompt: |
  Review this OpenAPI specification for:
  
  Design Consistency:
  - RESTful naming conventions
  - HTTP status code usage
  - Error response formats
  - Resource modeling patterns
  
  Technical Quality:
  - Pagination patterns
  - Filtering and sorting
  - Authentication/authorization
  - Rate limiting considerations
  
  Documentation Quality:
  - Parameter descriptions
  - Example requests/responses
  - Error scenario documentation
  
  OpenAPI Spec: {api_spec}
  
  Provide specific recommendations with examples.

# Integration with CI/CD
name: API Design Review
on:
  pull_request:
    paths: ['**/*.yaml', '**/*.yml']
    
jobs:
  api-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Review API Design
        run: |
          for file in $(find . -name "*.yaml" -o -name "*.yml"); do
            echo "Reviewing $file..."
            # Call LLM API for review
            python scripts/api_review.py "$file"
          done
```

**Results:**
- **Review time:** 2 hours → 30 minutes per API
- **Consistency:** 95% adherence to design standards
- **Velocity:** 40% faster API delivery
- **Quality:** 50% reduction in post-deployment API changes

### Database Schema Evolution with LLM
**Context:** E-commerce platform, PostgreSQL, complex table relationships  
**Problem:** Schema changes breaking downstream services  
**LLM Application:** Used GPT-4 to analyze migration impact  

**Implementation:**
```python
# Schema migration impact analysis
def analyze_migration_impact(current_schema, proposed_migration):
    analysis_prompt = f"""
    Analyze this database migration for breaking changes:
    
    Current Schema: {current_schema}
    Proposed Migration: {proposed_migration}
    
    Identify:
    1. Breaking changes and affected queries
    2. Data loss risks and mitigation strategies
    3. Performance impact on existing indexes
    4. Backward compatibility considerations
    5. Required application code changes
    
    Provide migration strategy with rollback plan.
    """
    
    response = openai.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": analysis_prompt}]
    )
    
    return response.choices[0].message.content

# Example usage
current_schema = """
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);
"""

migration = """
ALTER TABLE users 
ADD COLUMN phone VARCHAR(20),
ALTER COLUMN email DROP NOT NULL;
"""

impact_analysis = analyze_migration_impact(current_schema, migration)
```

**Results:**
- **Breaking changes prevented:** 8 out of 10 migrations
- **Rollback incidents:** Reduced from 3/month to 0.5/month
- **Migration confidence:** Team reports 85% more confidence in schema changes
- **Documentation:** 100% of migrations now have impact analysis

---

## Development Workflow Integration

### Code Review Assistant Implementation
**Context:** React/TypeScript frontend team, GitHub PR workflow  
**Problem:** Code reviews missing performance and accessibility issues  
**LLM Application:** GitHub Copilot + custom prompts for comprehensive review  

**Implementation:**
```javascript
// Enhanced code review automation
const reviewPrompt = `
Review this React component for:

Performance Issues:
- Unnecessary re-renders and missing memoization
- Expensive operations in render methods
- Memory leaks and cleanup issues
- Bundle size impact

Accessibility Compliance:
- ARIA labels and roles
- Keyboard navigation support
- Screen reader compatibility
- Color contrast and visual indicators

TypeScript Quality:
- Type safety and proper interfaces
- Error handling and edge cases
- Generic usage and constraints

Testing Coverage:
- Missing test scenarios
- Edge case validation
- Integration test needs

Component: {componentCode}
Test file: {testCode}

Provide specific, actionable recommendations.
`;

// GitHub Actions integration
async function enhancedCodeReview(prFiles) {
    for (const file of prFiles) {
        if (file.filename.endsWith('.tsx') || file.filename.endsWith('.ts')) {
            const review = await openai.chat.completions.create({
                model: "gpt-4",
                messages: [{
                    role: "user",
                    content: reviewPrompt.replace('{componentCode}', file.patch)
                }]
            });
            
            // Post review comments
            await github.pulls.createReviewComment({
                pull_number: pr.number,
                path: file.filename,
                body: `## 🤖 AI-Enhanced Review\n\n${review.choices[0].message.content}`,
                side: 'RIGHT',
                line: file.additions
            });
        }
    }
}
```

**Results:**
- **Defect detection:** 60% improvement in catching performance issues
- **Accessibility compliance:** 90% reduction in a11y violations
- **Review speed:** 25% faster while maintaining quality
- **Knowledge transfer:** Junior developers learned patterns 50% faster

### Documentation Generation Pipeline
**Context:** Node.js microservices, Express APIs, multiple teams  
**Problem:** API documentation constantly outdated  
**LLM Application:** Automated OpenAPI spec generation from code  

**Implementation:**
```javascript
// Automated documentation pipeline
class APIDocGenerator {
    constructor() {
        this.routes = [];
        this.schemas = [];
    }
    
    extractRoutes(codebase) {
        // Parse Express route definitions
        const routeFiles = glob.sync('src/routes/**/*.js', { cwd: codebase });
        
        routeFiles.forEach(file => {
            const content = fs.readFileSync(file, 'utf8');
            this.analyzeRouteFile(content, file);
        });
    }
    
    async generateDocumentation() {
        const docPrompt = `
        Generate OpenAPI 3.0 specification for these API routes:
        
        Routes: ${JSON.stringify(this.routes, null, 2)}
        
        Include:
        1. Complete request/response schemas
        2. Error responses with proper HTTP codes
        3. Authentication requirements
        4. Parameter validation rules
        5. Usage examples for each endpoint
        
        Follow OpenAPI 3.0 standards and our API design guidelines.
        `;
        
        const response = await openai.chat.completions.create({
            model: "gpt-4",
            messages: [{ role: "user", content: docPrompt }]
        });
        
        return YAML.parse(response.choices[0].message.content);
    }
}

// CI/CD integration
pipeline.stage('Generate API Docs') {
    script {
        def generator = new APIDocGenerator()
        generator.extractRoutes('.')
        def openApiSpec = generator.generateDocumentation()
        
        // Deploy to documentation site
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: 'docs',
            reportFiles: 'api-spec.html',
            reportName: 'API Documentation'
        ])
    }
}
```

**Results:**
- **Documentation accuracy:** 95% (was 30% with manual docs)
- **Developer onboarding:** New developers productive 2 days faster
- **API adoption:** 40% increase in internal API usage
- **Maintenance overhead:** 80% reduction in documentation maintenance time

---

## Quality Assurance Integration

### Test Case Generation Strategy
**Context:** Banking application, critical transaction flows  
**Problem:** Edge cases missed in manual test planning  
**LLM Application:** GPT-4 for comprehensive test scenario generation  

**Implementation:**
```python
# Comprehensive test generation framework
class TestCaseGenerator:
    def __init__(self):
        self.test_categories = [
            'happy_path',
            'edge_cases',
            'error_conditions',
            'security_boundaries',
            'performance_stress',
            'integration_scenarios'
        ]
    
    def generate_test_suite(self, feature_spec, existing_tests):
        test_prompt = f"""
        Generate comprehensive test cases for this banking feature:
        
        Feature Specification: {feature_spec}
        Existing Tests: {existing_tests}
        
        Generate test cases for:
        
        Happy Path Scenarios:
        - Standard user flows with valid inputs
        - Successful transaction completions
        - Normal business rule applications
        
        Edge Cases:
        - Boundary value testing (min/max amounts)
        - Unusual but valid input combinations
        - Race condition scenarios
        
        Error Conditions:
        - Invalid input handling
        - Network failure scenarios
        - Third-party service failures
        
        Security Testing:
        - Authorization boundary testing
        - Input validation security
        - Session management edge cases
        
        Format as Gherkin scenarios with Given/When/Then structure.
        Include test data and expected outcomes.
        """
        
        response = openai.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": test_prompt}]
        )
        
        return self.parse_gherkin_scenarios(response.choices[0].message.content)
    
    def parse_gherkin_scenarios(self, gherkin_text):
        # Parse generated Gherkin into executable test cases
        scenarios = []
        current_scenario = None
        
        for line in gherkin_text.split('\n'):
            if line.strip().startswith('Scenario:'):
                if current_scenario:
                    scenarios.append(current_scenario)
                current_scenario = {'name': line.strip(), 'steps': []}
            elif line.strip().startswith(('Given', 'When', 'Then', 'And')):
                current_scenario['steps'].append(line.strip())
        
        return scenarios
```

**Results:**
- **Test coverage:** Increased from 75% to 92%
- **Bug detection:** 45% more issues caught in testing
- **Production incidents:** Reduced by 30%
- **Test creation speed:** 3x faster than manual test writing

### Architecture Decision Record (ADR) Assistance
**Context:** Platform team, multiple technology choices  
**Problem:** ADRs taking too long to write, inconsistent format  
**LLM Application:** Template-driven ADR generation  

**Implementation:**
```markdown
# ADR Generation Workflow
class ADRGenerator:
    def __init__(self):
        self.template = """
        # ADR-{number}: {title}
        
        ## Status
        {status}
        
        ## Context
        {context}
        
        ## Decision
        {decision}
        
        ## Consequences
        ### Positive
        {positive_consequences}
        
        ### Negative
        {negative_consequences}
        
        ### Risks
        {risks}
        """
    
    def generate_adr(self, decision_context):
        adr_prompt = f"""
        Generate an Architecture Decision Record for:
        
        Decision Context: {decision_context['problem']}
        Options Considered: {decision_context['alternatives']}
        Constraints: {decision_context['constraints']}
        
        Create a comprehensive ADR that includes:
        
        1. Clear problem statement and context
        2. Alternatives evaluated with pros/cons
        3. Decision rationale with supporting evidence
        4. Positive and negative consequences
        5. Implementation risks and mitigation strategies
        6. Success metrics and review criteria
        
        Use our standard ADR template format.
        Focus on helping future developers understand the reasoning.
        """
        
        response = openai.chat.completions.create(
            model="gpt-4",
            messages=[{"role": "user", "content": adr_prompt}]
        )
        
        return response.choices[0].message.content

# Usage example
decision_context = {
    "problem": "Choose message queue technology for event-driven architecture",
    "alternatives": ["Apache Kafka", "RabbitMQ", "Amazon SQS", "Redis Streams"],
    "constraints": ["High throughput", "Horizontal scaling", "Operational simplicity"]
}

adr_content = generator.generate_adr(decision_context)
```

**Results:**
- **ADR completion time:** 4 hours → 1 hour
- **Decision quality:** Better consequence analysis and risk assessment
- **Team alignment:** 90% of team reads ADRs (was 60%)
- **Knowledge retention:** 85% improvement in architectural decision tracking

---

