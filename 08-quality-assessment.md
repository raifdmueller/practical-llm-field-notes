# Quality Assessment
---

## Practical Evaluation Tools in Production

### LLM-as-a-Judge Evaluation Framework
**Context:** Need scalable quality assessment for LLM outputs  
**Tool:** Use GPT-4 to evaluate other LLM responses  

**Implementation:**
```python
# Evaluation prompt template
judge_prompt = """
Evaluate this LLM response on a scale of 1-5 for:

1. **Accuracy**: Are the facts correct?
2. **Relevance**: Does it answer the actual question?
3. **Completeness**: Are key points covered?
4. **Clarity**: Is it easy to understand?
5. **Safety**: Are there any harmful elements?

Response to evaluate: "{response}"
Original query: "{query}"
Context provided: "{context}"

Provide scores and brief justification for each criterion.
Format: JSON with scores and reasoning.
"""

class LLMJudge:
    def __init__(self, evaluator_model="gpt-4"):
        self.evaluator_model = evaluator_model
    
    def evaluate_response(self, response, query, context=""):
        prompt = judge_prompt.format(
            response=response, 
            query=query, 
            context=context
        )
        
        evaluation = openai.chat.completions.create(
            model=self.evaluator_model,
            messages=[{"role": "user", "content": prompt}],
            temperature=0.1  # Low temperature for consistent evaluation
        )
        
        return json.loads(evaluation.choices[0].message.content)
```

**Results:** 85% correlation with human evaluation, 100x faster than manual review

### RAGAS for RAG System Quality
**Context:** Retrieval-Augmented Generation systems need specialized evaluation  
**Tool:** RAGAS framework for comprehensive RAG assessment  

**Implementation:**
```python
from ragas import evaluate
from ragas.metrics import (
    answer_relevancy,
    faithfulness,
    context_recall,
    context_precision
)

# Evaluation dataset format
dataset = {
    "question": ["What is the capital of France?"],
    "answer": ["Paris is the capital of France"],
    "contexts": [["France is a country in Europe. Paris is its capital city."]],
    "ground_truths": [["Paris"]]
}

# Run evaluation
result = evaluate(
    dataset,
    metrics=[answer_relevancy, faithfulness, context_recall, context_precision]
)

print(f"Answer Relevancy: {result['answer_relevancy']}")
print(f"Faithfulness: {result['faithfulness']}")
print(f"Context Precision: {result['context_precision']}")
print(f"Context Recall: {result['context_recall']}")
```

**Metrics Explained:**
- **Faithfulness**: How well the answer is grounded in the context (0-1)
- **Answer Relevancy**: How relevant the answer is to the question (0-1)
- **Context Precision**: How relevant the retrieved context is (0-1)
- **Context Recall**: How much of the ground truth is captured (0-1)

### DeepEval for Comprehensive Testing
**Context:** Need multiple evaluation metrics in one framework  
**Tool:** DeepEval with 14+ built-in metrics  

**Implementation:**
```python
from deepeval import evaluate
from deepeval.metrics import GEval, BiasMetric, ToxicityMetric
from deepeval.test_case import LLMTestCase

# Custom evaluation criteria
correctness_metric = GEval(
    name="Correctness",
    criteria="Determine whether the actual output is factually correct based on the expected output.",
    evaluation_steps=[
        "Check whether the facts in 'actual output' contradicts any facts in 'expected output'",
        "Heavily penalize omission of important facts",
        "Vague or ambiguous language should be penalized"
    ]
)

# Bias and safety evaluation
bias_metric = BiasMetric(threshold=0.5)
toxicity_metric = ToxicityMetric(threshold=0.7)

# Run comprehensive evaluation
test_case = LLMTestCase(
    input="Explain climate change effects",
    actual_output=actual_response,
    expected_output=expected_response
)

evaluate([test_case], [correctness_metric, bias_metric, toxicity_metric])
```

**Available Metrics:**
- **G-Eval**: Custom criteria evaluation
- **Answer Relevancy**: Question-answer alignment
- **Faithfulness**: Context grounding
- **Contextual Precision/Recall**: Retrieval quality
- **Hallucination**: False information detection
- **Bias/Toxicity**: Safety and fairness evaluation

---

## Real-Time Monitoring Solutions

### LangSmith for Production Monitoring
**Context:** Monitor LLM applications in production with tracing  
**Tool:** LangSmith for comprehensive observability  

**Implementation:**
```python
from langsmith import Client, traceable

# Setup tracing
client = Client()

@traceable
def rag_pipeline(query: str):
    # Retrieve relevant documents
    docs = retriever.get_relevant_documents(query)
    
    # Generate response
    response = llm.invoke(query, context=docs)
    
    # Log metrics
    client.create_run(
        name="rag_pipeline",
        inputs={"query": query},
        outputs={"response": response},
        extra={"context_length": len(docs)}
    )
    
    return response
```

**Monitoring Capabilities:**
- **Latency tracking**: P50, P95, P99 response times
- **Cost monitoring**: Token usage and API costs
- **Quality drift detection**: Response quality over time
- **User feedback integration**: Thumbs up/down tracking

### Custom Quality Dashboard
**Context:** Need business-specific quality metrics  
**Tool:** Custom dashboard for domain-specific evaluation  

**Implementation:**
```python
# Quality metrics calculation
class QualityDashboard:
    def __init__(self):
        self.metrics = {
            "hallucination_rate": 0.0,
            "user_satisfaction": 0.0,
            "response_relevance": 0.0,
            "safety_score": 0.0
        }
    
    def calculate_daily_metrics(self, responses, feedback):
        # Hallucination detection
        hallucinations = sum(1 for r in responses if self.detect_hallucination(r))
        self.metrics["hallucination_rate"] = hallucinations / len(responses)
        
        # User satisfaction from feedback
        positive_feedback = sum(1 for f in feedback if f.rating >= 4)
        self.metrics["user_satisfaction"] = positive_feedback / len(feedback)
        
        # Automated relevance scoring
        relevance_scores = [self.score_relevance(r) for r in responses]
        self.metrics["response_relevance"] = sum(relevance_scores) / len(relevance_scores)
        
        return self.metrics
    
    def detect_hallucination(self, response):
        # Implement hallucination detection logic
        # Could use fact-checking APIs, knowledge base verification, etc.
        pass
    
    def score_relevance(self, response):
        # Implement relevance scoring
        # Could use semantic similarity, keyword matching, etc.
        pass
```

---

## Code Quality Assessment Tools

### AST-Based Code Analysis
**Context:** Evaluate LLM-generated code quality systematically  
**Tool:** Python AST analysis for code structure assessment  

**Implementation:**
```python
import ast
from typing import Dict, List

class CodeQualityAnalyzer:
    def __init__(self):
        self.quality_metrics = {}
    
    def analyze_code(self, code: str) -> Dict[str, float]:
        try:
            tree = ast.parse(code)
            
            # Complexity analysis
            complexity = self.calculate_complexity(tree)
            
            # Structure analysis
            structure_score = self.analyze_structure(tree)
            
            # Security patterns
            security_score = self.check_security_patterns(tree)
            
            return {
                "complexity": complexity,
                "structure": structure_score,
                "security": security_score,
                "overall": (complexity + structure_score + security_score) / 3
            }
        except SyntaxError:
            return {"syntax_error": True, "overall": 0.0}
    
    def calculate_complexity(self, tree) -> float:
        # McCabe complexity calculation
        complexity = 1  # Base complexity
        for node in ast.walk(tree):
            if isinstance(node, (ast.If, ast.While, ast.For, ast.AsyncFor)):
                complexity += 1
            elif isinstance(node, ast.ExceptHandler):
                complexity += 1
        return min(complexity / 10, 1.0)  # Normalize to 0-1
    
    def analyze_structure(self, tree) -> float:
        # Analyze code structure quality
        functions = sum(1 for node in ast.walk(tree) if isinstance(node, ast.FunctionDef))
        classes = sum(1 for node in ast.walk(tree) if isinstance(node, ast.ClassDef))
        
        # Penalize very long functions
        long_functions = 0
        for node in ast.walk(tree):
            if isinstance(node, ast.FunctionDef):
                if len(node.body) > 20:  # Arbitrary threshold
                    long_functions += 1
        
        structure_score = 1.0
        if functions > 0:
            structure_score -= (long_functions / functions) * 0.5
        
        return max(structure_score, 0.0)
    
    def check_security_patterns(self, tree) -> float:
        # Check for common security issues
        security_issues = 0
        total_checks = 0
        
        for node in ast.walk(tree):
            if isinstance(node, ast.Call):
                # Check for dangerous function calls
                if hasattr(node.func, 'id'):
                    if node.func.id in ['eval', 'exec', 'compile']:
                        security_issues += 1
                    total_checks += 1
        
        if total_checks == 0:
            return 1.0
        return 1.0 - (security_issues / total_checks)
```

### Security Pattern Detection
**Context:** Ensure LLM-generated code follows security best practices  
**Tool:** Automated security pattern analysis  

**Implementation:**
```python
import re

class SecurityAnalyzer:
    def __init__(self):
        self.security_patterns = {
            "hardcoded_secrets": [
                r"password\s*=\s*['\"][^'\"]+['\"]",
                r"api_key\s*=\s*['\"][^'\"]+['\"]",
                r"secret\s*=\s*['\"][^'\"]+['\"]"
            ],
            "sql_injection": [
                r"execute\(['\"][^'\"]*%s[^'\"]*['\"]\)",
                r"query\(['\"][^'\"]*\+[^'\"]*['\"]\)"
            ],
            "unsafe_imports": [
                r"import\s+pickle",
                r"from\s+pickle\s+import",
                r"import\s+subprocess",
                r"eval\(",
                r"exec\("
            ]
        }
    
    def analyze_security(self, code: str) -> Dict[str, List[str]]:
        issues = {}
        for category, patterns in self.security_patterns.items():
            category_issues = []
            for pattern in patterns:
                matches = re.finditer(pattern, code, re.IGNORECASE)
                for match in matches:
                    line_num = code[:match.start()].count('\n') + 1
                    category_issues.append(f"Line {line_num}: {match.group()}")
            if category_issues:
                issues[category] = category_issues
        return issues
```

---

## A/B Testing Framework for LLM Outputs

### Prompt Variation Testing
**Context:** Compare different prompts or models systematically  
**Tool:** Statistical A/B testing framework  

**Implementation:**
```python
from scipy import stats
import numpy as np

class LLMAbTester:
    def __init__(self):
        self.experiments = {}
    
    def run_experiment(self, 
                      experiment_name: str,
                      variant_a_responses: List[str],
                      variant_b_responses: List[str],
                      quality_metric_func: callable) -> Dict:
        
        # Calculate quality scores
        scores_a = [quality_metric_func(response) for response in variant_a_responses]
        scores_b = [quality_metric_func(response) for response in variant_b_responses]
        
        # Statistical significance test
        t_stat, p_value = stats.ttest_ind(scores_a, scores_b)
        
        # Effect size (Cohen's d)
        pooled_std = np.sqrt(((len(scores_a) - 1) * np.var(scores_a) + 
                             (len(scores_b) - 1) * np.var(scores_b)) / 
                            (len(scores_a) + len(scores_b) - 2))
        cohens_d = (np.mean(scores_a) - np.mean(scores_b)) / pooled_std
        
        return {
            "experiment": experiment_name,
            "variant_a_mean": np.mean(scores_a),
            "variant_b_mean": np.mean(scores_b),
            "p_value": p_value,
            "significant": p_value < 0.05,
            "effect_size": cohens_d,
            "winner": "A" if np.mean(scores_a) > np.mean(scores_b) else "B"
        }

# Usage example
def quality_score(response):
    # Implement your quality scoring logic
    return len(response.split()) / 100  # Simple word count metric

ab_tester = LLMAbTester()
result = ab_tester.run_experiment(
    "prompt_comparison",
    variant_a_responses=["Response 1", "Response 2"],
    variant_b_responses=["Better response 1", "Better response 2"],
    quality_metric_func=quality_score
)
```

---

## Production Quality Monitoring Checklist

### Daily Quality Checks
```python
# Automated daily quality assessment
def daily_quality_check():
    checks = {
        "response_latency": check_latency_metrics(),
        "error_rate": calculate_error_rate(),
        "user_satisfaction": analyze_user_feedback(),
        "cost_efficiency": monitor_token_usage(),
        "hallucination_detection": run_hallucination_checks(),
        "safety_compliance": check_safety_violations()
    }
    
    # Alert if any metric exceeds threshold
    for metric, value in checks.items():
        if value < QUALITY_THRESHOLDS[metric]:
            send_alert(f"Quality degradation in {metric}: {value}")
    
    return checks

QUALITY_THRESHOLDS = {
    "response_latency": 2.0,  # seconds
    "error_rate": 0.05,       # 5%
    "user_satisfaction": 0.8,  # 80%
    "cost_efficiency": 0.02,   # $0.02 per query
    "hallucination_detection": 0.95,  # 95% accuracy
    "safety_compliance": 0.99  # 99% safe
}
```

### Quality Regression Detection
```python
class QualityRegressionDetector:
    def __init__(self, baseline_window=30, alert_threshold=0.1):
        self.baseline_window = baseline_window
        self.alert_threshold = alert_threshold
        self.historical_metrics = []
    
    def check_regression(self, current_metrics):
        if len(self.historical_metrics) < self.baseline_window:
            self.historical_metrics.append(current_metrics)
            return {"status": "collecting_baseline"}
        
        baseline_avg = np.mean(self.historical_metrics[-self.baseline_window:])
        current_avg = np.mean(current_metrics)
        
        regression_ratio = (baseline_avg - current_avg) / baseline_avg
        
        if regression_ratio > self.alert_threshold:
            return {
                "status": "regression_detected",
                "severity": "high" if regression_ratio > 0.2 else "medium",
                "regression_percentage": regression_ratio * 100
            }
        
        return {"status": "stable"}
```

---

## Code Quality Assessment

### Functionality Evaluation
**Primary Criteria:**
- **Correctness:** Does the code solve the stated problem?
- **Completeness:** Are all requirements addressed?
- **Edge Cases:** Are error conditions and boundary cases handled?
- **Testing:** Is the code testable and does it include tests?

**Validation Approach:**
```
1. Run the code in the target environment
2. Test with normal inputs and expected edge cases
3. Verify error handling with invalid inputs
4. Check performance with realistic data volumes
```

### Code Quality Indicators
**Style and Maintainability:**
- **Readability:** Clear variable names, logical structure
- **Consistency:** Follows established coding standards
- **Documentation:** Adequate comments and docstrings
- **Best Practices:** Uses language/framework conventions appropriately

**Architecture and Design:**
- **Modularity:** Appropriate separation of concerns
- **Reusability:** Components can be used in different contexts
- **Extensibility:** Easy to modify and extend
- **Dependencies:** Minimal and appropriate external dependencies

**Common LLM Code Issues:**
- Overly complex solutions to simple problems
- Missing input validation and error handling
- Use of outdated or non-existent APIs
- Security vulnerabilities (hardcoded secrets, SQL injection potential)
- Performance bottlenecks in generated algorithms

## Documentation Quality Assessment

### Content Evaluation
**Accuracy and Relevance:**
- **Factual Correctness:** Information matches current reality
- **Technical Accuracy:** Code examples work as written
- **Context Appropriateness:** Content matches intended audience
- **Currency:** Information reflects current best practices

**Structure and Clarity:**
- **Logical Organization:** Information flows logically
- **Completeness:** Covers all necessary aspects
- **Clarity:** Easy to understand for target audience
- **Actionability:** Provides clear, executable next steps

### Usability Factors
- **Findability:** Easy to locate relevant information
- **Scannability:** Key information stands out
- **Examples:** Includes practical, working demonstrations
- **Maintenance:** Can be easily updated as requirements change

## Validation Strategies

### Multi-Perspective Validation
**1. Technical Validation:**
- Automated testing (syntax, compilation, basic functionality)
- Code review by experienced developers
- Security scanning and vulnerability assessment
- Performance benchmarking

**2. Domain Expert Review:**
- Subject matter expert evaluation
- Industry best practice compliance
- Regulatory or compliance requirement verification
- Business logic validation

**3. User Acceptance Testing:**
- End-user functionality testing
- Usability and user experience evaluation
- Real-world scenario testing
- Documentation clarity verification

### Cross-Validation Techniques
**Prompt Consistency Testing:**
- Same request with different phrasings
- Multiple attempts with identical prompts
- Temperature variation impact assessment
- Different model comparison (if available)

**Temporal Consistency:**
- Results consistency over time
- Model update impact assessment
- Knowledge cutoff impact evaluation

## Quality Metrics and Scoring

### Quantitative Metrics
**Code Quality Scores:**
- Static analysis tool scores (linting, complexity)
- Test coverage percentage
- Performance benchmarks (execution time, memory usage)
- Security vulnerability counts

**Documentation Metrics:**
- Readability scores (Flesch-Kincaid, etc.)
- Completeness checklists
- User task completion rates
- Time-to-information metrics

### Qualitative Assessment
**Expert Review Criteria:**
- Technical accuracy (1-5 scale)
- Appropriateness for context (1-5 scale)
- Innovation and creativity (1-5 scale)
- Maintainability and extensibility (1-5 scale)

**User Experience Evaluation:**
- Clarity and understandability
- Practical applicability
- Learning curve assessment
- Error recovery effectiveness

## Red Flags and Warning Signs

### Code Quality Red Flags
- **Immediate Rejection Criteria:**
  - Code that doesn't compile or run
  - Obvious security vulnerabilities
  - Hardcoded credentials or sensitive data
  - Use of deprecated or non-existent APIs

- **Requires Review:**
  - Overly complex solutions to simple problems
  - Missing error handling or input validation
  - Inconsistent coding style
  - Performance bottlenecks or resource waste

### Content Quality Warning Signs
- **Accuracy Issues:**
  - Outdated information or broken links
  - Contradictory statements within the content
  - Technical details that can't be verified
  - Claims without proper attribution

- **Usability Problems:**
  - Vague or generic responses
  - Missing context or unstated assumptions
  - Inconsistent terminology or style
  - Steps that can't be practically followed

## Quality Improvement Strategies

### Iterative Refinement Process
1. **Initial Assessment:** Apply basic quality criteria
2. **Targeted Improvement:** Address specific weaknesses
3. **Expert Review:** Get domain specialist feedback
4. **User Testing:** Validate with real users
5. **Documentation:** Record lessons learned

### Prompt Engineering for Quality
**Quality-Focused Prompting:**
- Request specific coding standards compliance
- Ask for test cases and error handling
- Specify security considerations
- Request documentation and comments

**Example Quality Prompt:**
```
"Create a Python function that [requirement]. Include:
- Input validation and error handling
- Comprehensive docstring with examples
- Unit tests covering normal and edge cases
- Follow PEP 8 style guidelines
- Consider security implications"
```

### Quality Assurance Workflows
**Development Phase:**
- Code review checklists
- Automated testing integration
- Security scanning automation
- Documentation quality gates

**Post-Deployment:**
- User feedback collection
- Performance monitoring
- Update and maintenance tracking
- Quality metric trending

---

## Implementation Guidelines

### Getting Started Checklist
1. **Choose evaluation framework** based on your use case:
   - RAG systems → RAGAS
   - General LLM apps → DeepEval
   - Custom requirements → LLM-as-a-Judge

2. **Set up baseline metrics** before production deployment:
   - Response quality scores
   - Latency benchmarks  
   - Cost per interaction
   - User satisfaction baseline

3. **Implement monitoring pipeline**:
   - Real-time quality tracking
   - Automated alerting for degradation
   - Daily/weekly quality reports
   - A/B testing infrastructure

4. **Establish quality gates**:
   - Minimum quality scores for deployment
   - Automated rollback triggers
   - Human review requirements for edge cases

### Success Metrics to Track
- **Quality Consistency**: Standard deviation of quality scores over time
- **Regression Detection**: Time to detect quality degradation
- **User Satisfaction**: Net Promoter Score or similar feedback metrics
- **Cost Efficiency**: Quality score per dollar spent on LLM API calls
- **Deployment Confidence**: Percentage of deployments without rollbacks

---

## 🚀 Share Your Quality Methods

**What quality assessment techniques work in your context?**

We especially want:
- **Specific evaluation criteria** for your domain
- **Automated quality checking tools** and workflows
- **Quality failure examples** and lessons learned
- **Metrics that correlate** with real-world success

**[Contribute Your Quality Methods](CONTRIBUTING.md)**