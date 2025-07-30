# What Works Well

> **💡 Motivation:** Document success stories and proven practices to show where LLMs provide real value.
> 
> **📝 Content:** Concrete use cases that reliably deliver good results. Focus on reproducible successes.

> **⚠️ Initial Content:** These are example patterns based on common experiences to get started.
> 
> **We need YOUR specific success stories to make this section truly valuable!**

---

## Small Helper Scripts & Utilities
**Context:** Quick automation and utility script generation for specific, well-defined tasks

**Experience:** LLMs excel at creating focused scripts when you provide:
- Clear problem definition with concrete input/output examples
- Specific file formats and data structures
- Well-defined scope and constraints

**Successful examples:**
- Python script to iterate over `translations.yml` and translate missing entries
- Data processing utilities for CSV/JSON transformation
- One-off automation tasks for project maintenance
- File organization and batch processing scripts
- Simple CLI tools for repetitive development tasks

**Learnings:** 
- **Success factors:** Provide sample input data, expected output format, and specific error cases to handle
- **Best for:** Single-purpose scripts under 100 lines with clear requirements
- **Avoid:** Complex multi-step workflows or scripts requiring deep domain knowledge

**Cross-reference:** See [Effective Helper Script Prompting](04-tips-and-tricks.md#effective-helper-script-prompting) and [Rapid Prototyping Workflow](05-workflows.md#rapid-prototyping-with-helper-scripts)

## Template-Driven Development
**Context:** Using structured templates to guide consistent LLM output across similar tasks

**Experience:** Highly effective when you need:
- Standardized documentation (meeting notes, project specs, ADRs)
- Consistent code patterns (API endpoints, test cases, configuration files)
- Repeatable content creation (user stories, bug reports, deployment guides)

**Successful patterns:**
- User story creation with provided template structure
- Architecture Decision Records with standardized sections
- API documentation following OpenAPI specifications
- Test case templates for different testing scenarios
- Project scaffolding and boilerplate generation

**Learnings:**
- **Success factors:** Well-defined template structure with clear placeholders and examples
- **Templates work best when:** They have 3-7 clear sections with specific content expectations
- **Quality improves with:** Sample templates filled out correctly as references

**Cross-reference:** See [Template Design Patterns](04-tips-and-tricks.md#template-design-patterns) and [Template-First Development](05-workflows.md#template-first-development)

## Example-Based Code Generation
**Context:** Providing concrete examples to guide implementation and pattern replication

**Experience:** Dramatically improves results when you show LLMs:
- Working code examples that demonstrate the desired pattern
- API usage examples with actual request/response data
- Configuration examples from similar, successful setups

**Successful applications:**
- "Write a Python script like this [example], but for processing X instead of Y"
- API client generation based on working curl examples
- Configuration file creation using template from similar project
- Database migration scripts following established patterns
- Test case generation based on existing test examples

**Learnings:**
- **Success factors:** High-quality, relevant examples that closely match the target use case
- **Most effective:** When examples include edge cases and error handling
- **Quality key:** Examples should be complete, working, and well-commented

**Cross-reference:** See [Example Selection Strategies](04-tips-and-tricks.md#example-selection-strategies) and [Example-Driven Iteration](05-workflows.md#example-driven-iteration)

---

## Code Analysis & Review (Simple Cases)
**Context:** Daily development workflows for straightforward issues

**Experience:** LLMs work well for:
- Spotting obvious bugs and simple edge cases
- Basic code improvements and style suggestions
- Explaining what code does (ad-hoc explanations)
- Identifying common security patterns (SQL injection, XSS basics)

**Learnings:** Works best with simple, isolated code snippets. Complex architectural issues, subtle race conditions, or business-logic problems often require human expertise.

## Translation & Localization
**Context:** Converting content between languages

**Experience:** Highly effective for:
- Technical documentation translation
- User interface text and error messages
- Code comments and README files
- API documentation in multiple languages
- Marketing copy and user-facing content

**Learnings:** Provide context about target audience and technical domain. Review for cultural appropriateness and technical accuracy.

## Learning & Explanation
**Context:** Understanding new technologies or concepts

**Experience:** Excellent at:
- Breaking down complex technical concepts
- Providing step-by-step tutorials
- Explaining error messages and debugging steps
- Creating analogies and examples for difficult topics
- Ad-hoc code explanations ("What does this function do?")

**Learnings:** Best when you can iteratively refine the explanation depth and style.

## Template & Boilerplate Creation
**Context:** Starting new projects or standardizing approaches

**Experience:** Very reliable for:
- Creating project scaffolding and directory structures
- Generating configuration files (Docker, CI/CD, etc.)
- Building form templates and UI components
- Creating test frameworks and example data
- Standard document templates (meeting notes, reports)

**Learnings:** Specify your tech stack and constraints clearly for better results.

## Text Processing & Analysis
**Context:** Working with large amounts of text content

**Experience:** Strong capabilities for:
- Summarizing long documents and articles
- Extracting key information from unstructured text
- Converting between formats (Markdown, HTML, plain text)
- Proofreading and grammar checking
- Content classification and tagging

**Learnings:** Works best when you provide clear criteria for what constitutes important information.

---

## 🚀 Add Your Success Stories

**Have specific examples that worked well for you?** 

We especially want:
- **Quantifiable results** - time saved, quality improvements, error reductions
- **Specific techniques** - prompts, workflows, or approaches that consistently work
- **Context details** - team size, technology stack, industry constraints
- **Reproducible processes** - step-by-step approaches others can follow

**[Contribute Your Success Story](CONTRIBUTING.md)**