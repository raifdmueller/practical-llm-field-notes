# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with the Practical LLM Field Notes repository.

## Project Overview

This repository contains a structured collection of practical experiences with Large Language Models specifically focused on **software engineering and architecture contexts**. The content is community-driven and targets software developers, architects, engineering managers, and DevOps professionals.

### Repository Structure

- **01-what-works-well.md** - Success stories and proven practices (~20k lines)
- **02-what-doesnt-work-yet.md** - Current limitations that might improve
- **03-what-doesnt-work.md** - Fundamental limitations to be aware of
- **04-tips-and-tricks.md** - Practical techniques for maximizing LLM effectiveness (~21k lines)
- **05-workflows.md** - Main workflows file with sub-sections:
  - **05a-workflows-basic.md** - Basic workflow patterns
  - **05b-workflows-production.md** - Production-ready workflows
  - **05c-workflows-advanced.md** - Advanced integration approaches
- **06-pitfalls-and-lessons-learned.md** - Common mistakes and insights (~28k lines)
- **07-use-cases-software-architecture-development.md** - Main use cases file with sub-sections:
  - **07a-development-use-cases.md** - Development-focused applications
  - **07b-architecture-use-cases.md** - Architecture and design use cases
  - **07c-enterprise-use-cases.md** - Enterprise-scale applications
- **08-quality-assessment.md** - Methods for evaluating LLM outputs (~23k lines)
- **10-interesting-sessions.md** - Notable interactions and case studies

## Content Guidelines

### Target Audience Focus
**CRITICAL**: This repository focuses exclusively on software engineering and architecture applications. Do NOT add content for:
- General business applications
- Marketing or sales use cases
- Non-technical domains
- Academic research without practical software engineering application

### Content Structure Standards

#### Required Format for New Entries
All contributions must follow this structure:

```markdown
### [Descriptive Title]

**Context:** 
- Clear description of the situation
- Technology stack and constraints
- Team size and experience level

**Challenge:**
- Specific problem or goal
- Why traditional approaches fell short

**LLM Approach:**
- Specific prompts, tools, or techniques used
- Model(s) and configuration details
- Integration with existing workflows

**Results:**
- Measurable outcomes (time saved, quality improvements, etc.)
- Code samples or examples (anonymized)
- Before/after comparisons when applicable

**Key Learnings:**
- What worked well and why
- What didn't work as expected
- Actionable advice for others
- Specific dos and don'ts
```

#### File Size Management
- Keep individual files under 30k lines for readability
- When a file grows too large, split into focused sub-sections
- Use the main file as an index pointing to sub-sections
- Maintain consistent cross-references between related sections

### Quality Standards

#### Content Requirements
- **Specific and Actionable**: Avoid vague generalizations
- **Real-World Context**: Include actual project constraints and outcomes
- **Measurable Results**: Provide concrete metrics when possible
- **Anonymized Examples**: Share code samples and workflows without exposing sensitive information
- **Balanced Perspective**: Include both successes and failures

#### Technical Accuracy
- Verify all code examples work as presented
- Include version numbers for tools and models
- Specify exact prompts and configurations used
- Validate that examples follow current best practices

## Development Workflow

### Content Creation Process
1. **Research Phase**: Check existing content to avoid duplication
2. **Structure Planning**: Determine appropriate section and format
3. **Content Development**: Follow the required format structure
4. **Review and Validation**: Ensure technical accuracy and anonymization
5. **Integration**: Add cross-references and update related sections

### File Modification Guidelines
- **Before editing large files**: Read the entire section context
- **Maintain consistency**: Follow existing formatting and style
- **Update cross-references**: When adding new content, add references from related sections
- **Preserve anonymization**: Remove or obfuscate company names, proprietary information

### Version Control Practices
- Create descriptive commit messages focusing on the value added
- Use semantic prefixes: `feat:`, `fix:`, `docs:`, `refactor:`
- Example: `feat: Add microservices testing automation case study to development use cases`

## Common Tasks

### Adding New Content
```bash
# 1. Determine the appropriate section
# 2. Check for existing similar content
grep -r "keyword" *.md

# 3. Add content following the required format
# 4. Update related cross-references
# 5. Verify file size limits
wc -l [filename].md
```

### Content Reorganization
```bash
# Check file sizes
wc -l *.md | sort -n

# Split large files when they exceed ~25k lines
# Create focused sub-sections with clear naming
# Update main file to reference sub-sections
```

### Quality Assurance
- Verify all code examples are syntactically correct
- Check that all links and references work
- Ensure anonymization is complete
- Validate that content fits the software engineering focus

## Integration with Community

### GitHub Discussions Integration
- Reference relevant discussions in content
- Encourage community validation of new content
- Use discussions for content planning and feedback

### Contributing Workflow
- Follow CONTRIBUTING.md guidelines
- Engage with community through discussions before major additions
- Provide context for why specific content adds value

## Maintenance Tasks

### Regular Maintenance
- Monitor file sizes and split when necessary
- Update cross-references when reorganizing content
- Refresh outdated technical information
- Validate that links and examples still work

### Content Curation
- Remove or update deprecated information
- Consolidate duplicate or similar entries
- Improve organization based on usage patterns
- Maintain focus on software engineering applications

## Special Considerations

### Anonymization Requirements
- Remove company names, proprietary system names
- Generalize specific technical details that could identify organizations
- Replace specific metrics with ranges or relative improvements
- Ensure code examples don't contain sensitive information

### Technical Stack Diversity
- Include examples from various programming languages
- Cover different architectural patterns and scales
- Address both startup and enterprise contexts
- Balance traditional and modern technology stacks

### Measurable Impact Focus
- Prioritize content with quantifiable outcomes
- Include time savings, quality improvements, team productivity gains
- Provide before/after comparisons when possible
- Focus on business value alongside technical achievements

---

This repository serves as a practical knowledge base for software engineering teams. Maintain high quality standards and focus on actionable insights that help teams succeed with LLM integration in their development workflows.
