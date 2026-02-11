# GitHub Copilot Optimization & Configuration Guide

## Objective

Improve Copilot accuracy, maintain architectural consistency, and
increase productivity.

------------------------------------------------------------------------

## 1. Instruction Files

Create `.github/copilot-instructions.md`

Example:

-   Use service layer architecture
-   Do not access DB from controller
-   Use TypeScript strict mode
-   Use async/await only
-   Use Zod for validation

Keep instructions explicit and concise.

------------------------------------------------------------------------

## 2. Improve Project Context

Copilot learns from: - README - Folder structure - File naming -
Existing patterns

Best Practices: - Maintain consistent naming - Add sample
implementations - Document architecture clearly

------------------------------------------------------------------------

## 3. Better Prompting

Weak: // create api

Strong: // Create an Express route using Zod validation // Follow
service-layer architecture // Return standardized JSON response

More context = better output.

------------------------------------------------------------------------

## 4. MCP Integration Best Practices

-   Define strict JSON schemas
-   Avoid generic tool names
-   Return structured responses
-   Provide usage examples

------------------------------------------------------------------------

## 5. Optimization Checklist

-   [ ] Instruction file created
-   [ ] Architecture documented
-   [ ] Naming conventions defined
-   [ ] Validation pattern standardized
-   [ ] Tool schemas documented

------------------------------------------------------------------------

## Final Goal

Make Copilot a structured coding assistant aligned with your
architecture --- not just autocomplete.
