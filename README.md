# Design Critique – `Labeller.java` (Zingg)

## Context

This document is an independent design review of the `Labeller` class from the
Zingg open-source repository:


The review was written as part of a backend engineering assessment to evaluate
code understanding, design thinking, and extensibility considerations.

---

## Scope of Review

The critique focuses on the following areas:

- Class responsibility and separation of concerns
- Method design and readability
- Dependency management and coupling
- Error handling and resource management
- Extensibility for non-CLI labeling workflows
- Testability and maintainability

The analysis is limited to the provided class and its direct interactions, not
the entire Zingg codebase.

---

## Summary of Observations

The `Labeller` class plays a central role in orchestrating the CLI-based labeling
workflow. While the overall intent and data flow are clear, the class currently
combines multiple responsibilities such as:

- Workflow orchestration
- User interaction (CLI input/output)
- Data access and transformation
- Business logic for label updates
- Statistics tracking and reporting

This concentration of responsibilities increases cognitive load and makes the
class harder to extend, test, or adapt to alternate labeling interfaces.

---

## Design Critique Document

The full design critique discusses:

- Where the current design works well
- Specific design issues observed in the class
- How responsibility boundaries could be improved
- Suggested refactoring directions and abstractions
- How the class could be redesigned for better extensibility

Please refer to the main document for detailed analysis and recommendations.

---

## Author Notes

- This review reflects my own understanding and interpretation of the code.
- The observations are based on practical backend engineering experience and
  commonly applied design principles.
- No automated tools were used to generate the critique.

---

## Repository Reference

Zingg GitHub Repository:  
[https://github.com/zinggAI/zingg](https://github.com/pintukrgupta/zingg-labeller-design-review)
