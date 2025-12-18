# Design Critique: `Labeller.java`

## Introduction

This document presents my design review of the `Labeller` class located at:


The class is responsible for orchestrating the CLI-based labeling phase in the
Zingg training workflow. My review focuses on understanding the intent of the
existing implementation, identifying design trade-offs, and suggesting
improvements from a maintainability and extensibility perspective.

---

## High-Level Understanding of the Class

At a high level, `Labeller` performs the following tasks:

- Reads marked and unmarked training records
- Applies preprocessing on unmarked data
- Iterates over candidate record pairs
- Displays records and predictions to the user
- Collects labeling input via CLI
- Updates labeled records and statistics
- Writes the final labeled output

The overall flow is easy to follow, particularly in the `execute()` method,
which provides a clear top-down view of the labeling lifecycle.

---

## What Works Well

### Clear Workflow Orchestration

The `execute()` method acts as a readable entry point and clearly communicates
the sequence of operations involved in the labeling phase. This makes the class
approachable for someone new to the codebase.

### Use of Interfaces for Key Dependencies

Dependencies such as `ITrainingDataModel` and `ILabelDataViewHelper` are modeled
as interfaces, which indicates an intention toward abstraction and future
extensibility.

### Generic Design for Multiple Data Backends

The extensive use of generics (`<S, D, R, C, T>`) allows the same labeling logic
to operate across different execution engines and data representations, which
is a strong architectural choice for a framework-level component.

---

## Design Issues and Improvement Areas

### 1. Multiple Responsibilities in a Single Class

The `Labeller` class currently handles:

- Workflow coordination
- CLI interaction
- Business logic for labeling
- Data access and transformation
- Statistics tracking

Combining these concerns makes the class large and harder to evolve. For example,
introducing a non-CLI labeling interface would require changes across several
methods.

**Improvement:**  
I would separate the labeling workflow from the user interaction layer by
introducing a dedicated labeling session or workflow class, and keep UI-specific
logic behind an interface.

---

### 2. Tight Coupling to CLI Input

Methods such as `processRecordsCli()` and `readCliInput()` directly depend on
`System.in` and synchronous user input. This design tightly couples the labeling
logic to a CLI-based execution model.

**Improvement:**  
I would introduce an abstraction (e.g. `LabelDecisionProvider`) responsible for
returning labeling decisions. CLI input would be one implementation, while other
implementations could support UI-based or automated labeling.

---

### 3. Large, Complex Methods

`processRecordsCli()` contains iteration logic, prediction handling, display
logic, user input handling, and record updates in a single method. This increases
cognitive load and makes the method difficult to test in isolation.

**Improvement:**  
I would break this method into smaller, intention-revealing methods such as:
- preparing the labeling context
- processing a single record pair
- handling user decisions
- determining termination conditions

---

### 4. Error Handling and Logging

The class frequently catches generic `Exception` and sometimes prints stack
traces directly. In addition, expected conditions (such as no marked records)
are logged as warnings.

**Improvement:**  
I would distinguish between expected states and true failures, avoid generic
exception handling, and rely on structured logging instead of printing stack
traces.

---

### 5. Resource Management

The `readCliInput()` method creates a new `Scanner` instance on every call and
does not close it. While this may not cause immediate issues, it is inefficient
and makes the method harder to control in tests.

**Improvement:**  
I would inject a reusable input reader at the session level and decouple it from
`System.in`.

---

### 6. Lazy Initialization of Dependencies

Concrete implementations of `ITrainingDataModel` and `ILabelDataViewHelper` are
created inside getter methods. This hides dependencies and makes the class harder
to test or extend.

**Improvement:**  
I would prefer constructor-based dependency injection and delegate object
creation to a factory or configuration layer.

---

### 7. Use of Magic Numbers

User input options such as `9` for quitting labeling are represented as magic
numbers. This reduces readability and increases the chance of misuse.

**Improvement:**  
I would introduce a small domain-level enum to represent labeling decisions
explicitly.

---

## How I Would Design This Component

If I were designing this component from scratch, I would structure it around:

- A labeling workflow or session controller
- A decision provider abstraction (CLI, UI, automated)
- A data access layer for labeled and unlabeled records
- A statistics service for tracking progress
- A rendering layer responsible for display only

This separation would allow the core labeling logic to remain independent of the
input mechanism and make the system easier to test and extend.

---

## Closing Notes

The current `Labeller` implementation is functional and demonstrates a strong
understanding of the labeling domain. The primary opportunity for improvement
lies in separating concerns and reducing coupling between workflow logic and
user interaction. Addressing these areas would improve maintainability while
preserving the existing behavior.

This critique reflects my independent analysis of the code and design choices.
