# Template Documentation Rules

## Overview

This document defines the rules and standards for creating documentation for Zypin templates. All templates must follow these guidelines to ensure consistency, clarity, and usability.

## Required Documentation Files

Every template must include exactly **3 documentation files**:

1. **README.md** - Why use this template?
2. **WRITE_GUIDE.md** - How to use this template?
3. **DEBUG_GUIDE.md** - How to debug when encountering errors?

## File Structure and Content Rules

### README.md Rules

**Purpose**: Answer "Why should I use this template?"

**Content Requirements**:
- **Title**: Template name
- **Why Use This Template?** section with benefits
- **What You Get** section listing features
- **Quick Start** section with basic commands
- **Need Help?** section with links to other guides

**Length**: Maximum 60 lines
**Tone**: Marketing-focused, benefit-driven
**Format**: Use emojis for visual appeal

**Example Structure**:
```markdown
# Template Name

## Why Use This Template?

### 🎯 Benefit 1
### ⚡ Benefit 2
### 🔧 Benefit 3

## What You Get
- Feature 1
- Feature 2

## Quick Start
```bash
# Commands here
```

## Need Help?
- **Writing**: See `WRITE_GUIDE.md`
- **Debugging**: See `DEBUG_GUIDE.md`
```

### WRITE_GUIDE.md Rules

**Purpose**: Answer "How do I use this template?"

**Content Requirements**:
- **Writing Your First Test** section with example
- **Available Step Definitions** (if applicable)
- **Running Tests** section with commands
- **Best Practices** section
- **Project Structure** overview

**Length**: Maximum 200 lines
**Tone**: Tutorial-focused, step-by-step
**Format**: Code examples with explanations

**Example Structure**:
```markdown
# How to Use This Template

## Writing Your First Test
[Example code]

## Available Step Definitions
[Code examples]

## Running Tests
[Commands]

## Best Practices
[Guidelines]

## Project Structure
[File structure]
```

### DEBUG_GUIDE.md Rules

**Purpose**: Answer "How can I debug when I encounter errors?"

**Content Requirements**:
- **Quick Fixes for Common Issues** section
- **Debug Commands** section
- **Built-in Debug Features** section
- **Troubleshooting Checklist**
- **Quick Reference** section

**Length**: Maximum 220 lines
**Tone**: Problem-solving focused
**Format**: Error → Solution pairs

**Example Structure**:
```markdown
# How to Debug When You Encounter Errors

## Quick Fixes for Common Issues
### 🚨 Issue 1
**Error**: [Error message]
**Quick Fix**: [Solution]

## Debug Commands
[Commands]

## Built-in Debug Features
[Features]

## Troubleshooting Checklist
[Checklist]

## Quick Reference
[Reference]
```

## Writing Standards

### Language
- **Primary Language**: English only
- **Tone**: Professional but friendly
- **Style**: Clear, concise, actionable

### Formatting
- Use emojis for visual appeal (🎯, ⚡, 🔧, 🚨, etc.)
- Use code blocks for all commands and examples
- Use bullet points for lists
- Use headers for section organization

### Code Examples
- Always include working code examples
- Use realistic, practical examples
- Include both simple and advanced examples
- Provide complete, runnable commands

### Links and References
- Cross-reference between the 3 files
- Use relative links to other guide files
- Include external links only when necessary

## Content Guidelines

### README.md Content
- Focus on **benefits** and **value proposition**
- Highlight **unique features** of the template
- Include **quick start** to get users running immediately
- Keep it **high-level** and **overview-focused**

### WRITE_GUIDE.md Content
- Provide **step-by-step instructions**
- Include **practical examples**
- Cover **common use cases**
- Explain **best practices**
- Show **project structure**

### DEBUG_GUIDE.md Content
- Address **common problems** first
- Provide **quick fixes** for each issue
- Include **troubleshooting steps**
- Offer **debugging tools** and commands
- Provide **reference information**

## Quality Checklist

Before publishing any template documentation, verify:

- [ ] All 3 required files are present
- [ ] Each file is within the specified line limits
- [ ] Content answers the specific question for each file
- [ ] Code examples are complete and runnable
- [ ] Links between files work correctly
- [ ] Language is clear and professional
- [ ] Formatting is consistent
- [ ] Emojis are used appropriately
- [ ] No typos or grammatical errors

## Template-Specific Adaptations

### For Testing Templates
- Include test examples and step definitions
- Cover different testing scenarios
- Explain test execution and reporting

### For Framework Templates
- Include setup and configuration steps
- Cover integration with other tools
- Explain customization options

### For Utility Templates
- Include usage examples
- Cover different use cases
- Explain configuration options

## Maintenance Rules

### Updates
- Update documentation when template features change
- Keep examples current and working
- Update links and references as needed

### Version Control
- Document changes in commit messages
- Tag documentation updates with template versions
- Maintain backward compatibility in examples

### Review Process
- All documentation must be reviewed before publishing
- Test all code examples to ensure they work
- Verify all links and references are correct

## Examples of Good Documentation

### Good README.md
- Clear value proposition
- Quick start that works
- Links to other guides
- Appropriate length

### Good WRITE_GUIDE.md
- Step-by-step tutorial
- Working code examples
- Best practices included
- Project structure explained

### Good DEBUG_GUIDE.md
- Common issues addressed
- Quick fixes provided
- Debug tools explained
- Reference information included

## Anti-Patterns to Avoid

### README.md Anti-Patterns
- Too much technical detail
- Missing quick start
- No clear value proposition
- Overly long explanations

### WRITE_GUIDE.md Anti-Patterns
- Missing examples
- Unclear instructions
- No best practices
- Incomplete code snippets

### DEBUG_GUIDE.md Anti-Patterns
- No quick fixes
- Missing common issues
- Unclear troubleshooting steps
- No reference information

## Conclusion

Following these rules ensures that all Zypin templates have consistent, helpful, and user-friendly documentation. The three-file structure provides clear separation of concerns while maintaining cross-references for easy navigation.

Remember: The goal is to help users understand why to use the template, how to use it, and how to debug issues - all in a concise, actionable format.
