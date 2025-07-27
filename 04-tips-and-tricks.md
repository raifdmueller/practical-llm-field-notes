# Tips & Tricks

> **💡 Motivation:** Collect practical techniques that increase effectiveness when working with LLMs.
> 
> **📝 Content:** Prompt engineering, workflow optimizations, hidden features, workarounds.

## Prompt Engineering

### Context-First Approach
```
BAD: "Write a function to sort data"
GOOD: "I'm building a Python web app that processes user uploads. 
Write a function that sorts a list of dictionaries by timestamp, 
handling missing dates gracefully."
```

### Step-by-Step Instructions
```
Instead of: "Create a complete authentication system"
Try: "Let's build authentication step by step:
1. First, create a user registration form
2. Then we'll add password hashing
3. Finally, implement login sessions"
```

### Role-Based Prompting
```
"Act as a senior software architect reviewing this code. 
Focus on scalability, maintainability, and security concerns."
```

## Context Management

### Template for Complex Requests
```
**Context:** [What you're building]
**Goal:** [What you want to achieve]
**Constraints:** [Technical limitations, requirements]
**Format:** [How you want the output structured]
**Example:** [Show a similar example if available]
```

### Iterative Refinement
- Start with a broad request
- Ask for specific improvements
- Build complexity gradually
- Save working versions before major changes

## Code-Specific Tips

### Specify Your Stack
```
"Using React 18 with TypeScript, Tailwind CSS, and Vite..."
```

### Request Explanations
```
"Add comments explaining the key logic and potential edge cases"
```

### Ask for Alternatives
```
"Show me 2-3 different approaches to solve this, with pros/cons"
```