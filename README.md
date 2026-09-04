# tinyDSL

> a lightweight pattern language for communicating structured instructions, context, and guardrails to language models

When I first started using tinyDSL, I had no intention of creating a language. It began as a simple request asking an AI to make a prompt more compact. Among the formats it suggested was an informal layout based on YAML. It was familiar and avoided unnecessary symbols, so I ran with it. Over time, as I used it across tasks, schemas, and configurations, that shorthand evolved through trial and error into a system with its own strategies and principles. Even now as I document it, I am still not totally sure how to describe what this is. Is it a microlanguage? A fluid DSL? Describing it as a pattern language probably hits the mark. In any case, I know what it is not: it is not a formal programming language, and it requires no compilers or software parsers. It is simply a practical notation system built to give language models clear structural coordinates without the overhead.

## Why tinyDSL?

When working with language models, conversational prose invites too much interpretation. Open-ended paragraphs force a model to read between the lines, causing instructions to drift and produce inconsistent outcomes across separate prompts. In that noise, essential guardrails like negative constraints and file boundaries frequently get lost. Yet wrapping prompts in rigid data formats like JSON introduces unnecessary syntactic overhead and is poorly suited for qualitative context. tinyDSL solves this by replacing conversational instructions with compact, declarative boundary structures. It isolates prohibitions, anchors target outcomes, and provides a predictable, repeatable format that models can interpret consistently across tasks.

### Key Improvements

* **Focus on the Desired End State:** Describe what the finished deliverable must look like rather than micromanaging the process. Defining the target outcome prevents the model from improvising unnecessary intermediate steps.
* **Quarantine Prohibitions Separately:** Separate what the model must not do into its own dedicated container. This prevents the model from confusing forbidden boundaries with suggested actions.
* **Blend Precision with Nuance:** Apply structured keys for rules and interfaces, while keeping qualitative explanations in plain prose. Not every thought needs to be forced into a key-value pair.
* **Zero Tooling Dependencies:** tinyDSL has no compiler or software parser. It relies on intuitive indentation and layout that models and humans read naturally.


## How tinyDSL Works
 
> **The full notation and pattern reference lives in [`tinydsl.yaml`](./tinydsl.yaml)
> and [`macros.yaml`](./macros.yaml). This section explains how the pieces fit
> together — see those files for the complete, authoritative catalog.**
 
tinyDSL is built from three layers, each composing into the next:
 
- **Elements** — the smallest units: keys, literals, and a few directional
  operators (`->` for sequence, `>` for priority, `←` for lineage).
- **Shapes** — demonstrate ways to layout information: inline, narrative, or
  structured; compact or expanded. Sets of shapes for conveying types of information are defined. If a preference or need emerges for more detailed information, or compact expression, etc., there are alternative shapes in a set that one can "shift" to.
- **Patterns** — reusable solutions built from elements arranged into shapes,
  at three scales:
  - **Micros** — single-property building blocks
  - **Mods** — a few micros assembled to solve one functional problem
  - **Macros** — full-document blueprints, assembled from mods and micros

The provided pattern library is based on Elements and Shapes that have been used successfully after trial and error. Most of them represent concepts that are common properties to express when defining tasks for LLMs, writing instructions, or configurations.

The pattern library uses certain conventions intended to provide guidance on writing in tinyDSL with flexibility. Brackets like `[a | b]` mark a choice between terms; `<placeholder>` marks a slot you fill in. See `tinydsl.yaml` for the complete notation key.


 
### Patterns
 
The same small pattern reused, unchanged, at increasing scale:
 
**As a micro** — a standalone guardrail:
```yaml
prohibited:
  - "<forbidden_action_or_addition>"
```
 
**Inside a mod** — the same block, now paired with related micros to form a
complete guardrail unit:
```yaml
constraints:
  preserve_<aspect_to_lock>: true
  preserve:
    - "<element_or_logic_to_keep_intact>"
  prohibited:
    - "<forbidden_action_or_addition>"
```

 
**Inside a macro** — the same mod, now living inside a full task
specification:
```yaml
task:
  name: "<task_name_or_identifier>"
  ...
constraints:
  preserve_<aspect_to_lock>: true
  preserve:
    - "<element_or_logic_to_keep_intact>"
  prohibited:
    - "<forbidden_action_or_addition>"
...
```


_(Only part of the macro is shown)_
 

 
### Design Philosophy
 The patterns collected here are not universal laws. They are modular starting seeds refined across many real-world prompts. Because tinyDSL is built on the idea that structure follows information, you never have to force a task into an arbitrary layout. You can use these patterns as they are, adjust them, or let your AI invent new domain keys on the spot. In short, tinyDSL exists so you can create your own tinyDSL.
 
 
## Quickstart
  
### Files in this repo
 
- **`tinydsl.yaml`** — the core specification: notation, elements, shapes,
  and micro/modular patterns. Load this to give a model the full pattern
  vocabulary. It's ready to copy/paste into system instructions or be provided in whatever way is more convenient for you.
- **`macros.yaml`** *(optional)* — full-document blueprints. Include this
  alongside `tinydsl.yaml` if you want the model working from complete
  worked examples. Recommended if it's your first time using tinyDSL: the more comprehensive usage examples might be helpful examples for generating tinyDSL on brand-new topics.
- **`README.md`** — this file; orientation only, not required at runtime.
### Using it
 
 1. Just copy it into a system prompt, or a regular prompt, or upload tinydsl.yaml to a model. (Change it to a txt or md file if you need to, this doesn't actually NEED to be saved as .yaml.) Don't forget to include macros.yaml if that's your intention! just provide it the same way.

 2. Just prompt the LLM with the source material that you want it to translate into tinyDSL, if you have it ready. Or, proceed to develop the instructions you want translated into tinyDSL collaboratively with the model. It can show you how something looks in tinyDSL at any time. (It's really simple, guys)
 
 ---
### Example 1 
A minimal example:
 
**Plain instruction:**
"Update the billing module's error handling, but don't touch the public
API signatures or add new dependencies."
 
**As tinyDSL:**
```yaml
task:
  description: "Update error handling in the billing module."
constraints:
  preserve:
    - "public_api_method_signatures"
  prohibited:
    - "new_dependencies"
```
---
### Example 2
In practical use, tinyDSL instructions have less often been the result of a single translation from another source and have more often been the result of tweaking and optimizing over several iterations. Because properties being communicated in instructions are atomized explicitly instead of built into sentences, improving instructions or correcting failures is usually just a matter of adding another line here or value there. Some of the most complex tinyDSL texts are configuration files that weren't translated from a main source, but grown as configuration files.

See an iterative example below of how instructions for a coding agent were changed to troubleshoot failures in its implementation.

<div style="border-top: 2px dotted #bbb; margin: 20px 0;"></div>

#### Iteration 1: The Abstract Baseline (10:59 AM)
The initial goal was to enforce a standardized six-section structural layout across all generated modules:
```yaml
# Note: Reconstructed from the structural goals set at 10:59 AM
spec_version: 1.1
task_id: B2.2-Normalize
outputs:
  modify:
    - path: "[MODULE_DIR]/[TARGET_SUBMODULE]/[TARGET_FILE].py"
      normalize:
        sections:
          - Imports
          - "[WORK_UNIT_CLASS]"
          - "[SPECIALISTS]"
          - "[OBJECTIVES]"
          - Public Interface
          - "[WORK_UNIT_OBJECT]"
constraints:
  preserve_behavior: true
  preserve_section_comments: true
  preserve_blank_line_spacing: true
prohibited:
  - import_reorganization
  - formatting_changes_unrelated_to_this_task
```
**The Observed Failure:** The model claimed success but did not actually process what the section names meant
. Lacking concrete landmarks, it guessed where to put the comments and duplicated part of the comment header inside an adjacent generated file.

<div style="border-top: 2px dotted #bbb; margin: 20px 0;"></div>



#### Iteration 2: Adding Anchors and Structural Prohibitions (11:34 AM)
The next attempt aimed to stop the model from guessing where sections belonged
. Section headers were treated as structural markers rather than style choices
. The instruction set was updated to introduce positional anchors (to locate target regions) and explicit prohibitions against duplicate headers
.
```yaml
# Revised Spec at 11:34 AM
execution_context:
  load:
    - "[MODULE_DIR]/[ANCHOR_SUBMODULE]/[CONTEXT_FILE].py"
  modify:
    - "[MODULE_DIR]/[ANCHOR_SUBMODULE]/[CONTEXT_FILE].py"

transformation_spec:
  normalize:
    sections:
      - name: Imports
        anchor: top
      - name: "[WORK_UNIT_CLASS]"
        anchor: before_first_class_declaration
      - name: "[SPECIALISTS]"
        anchor: "before_first_[DECORATOR_A]"
      - name: "[OBJECTIVES]"
        anchor: "before_first_[DECORATOR_B]"
      - name: Public Interface
        anchor: before_first_public_method
      - name: "[WORK_UNIT_OBJECT]"
        anchor: "before_[DECORATOR_C]"

  constraints:
    exactly_once: [Imports, "[WORK_UNIT_CLASS]", "[SPECIALISTS]", "[OBJECTIVES]", Public Interface, "[WORK_UNIT_OBJECT]"]

  prohibited:
    - duplicate_section_headers
    - member_reordering
    - formatting_changes_unrelated_to_this_task
```
**Failure, again:** This constraint system was more precise, but exposed the limits of describing formatting procedurally
. The model left empty comment lines (raw # characters with no text) because the exact visual shape of the comment blocks was still described abstractly in natural language.

<div style="border-top: 2px dotted #bbb; margin: 20px 0;"></div>

#### Iteration 3: The Transition to Literal Templates (11:37 AM)
To eliminate abstract descriptions of whitespace and structure, the instruction set evolved to carry literal templates for the model to copy exactly.
```yaml
# Revised Spec at 11:37 AM
transformation_spec:
  normalize:
    sections:
      - name: "[SPECIALISTS]"
        anchor: "before_first_[DECORATOR_A]"
        literal_template: |
          # ==============================================================================
          # [SPECIALISTS]
          # ==============================================================================
      - name: "[OBJECTIVES]"
        anchor: "before_first_[DECORATOR_B]"
        literal_template: |
          # ==============================================================================
          # [OBJECTIVES]
          # ==============================================================================

  constraints:
    preserve_behavior: true

  verify:
    exact_match: ["[SPECIALISTS]", "[OBJECTIVES]"]
```
**The Result:** The model regressed. In attempting to satisfy the strict "anchor" and "literal template" rules, it rewrote unrelated surrounding code, inadvertently deleting the module's actual import blocks.

<div style="border-top: 2px dotted #bbb; margin: 20px 0;"></div>

#### Iteration 4: Surgical, Idempotent Conformance (11:39 AM)
This regression led to a major change in the execution strategy. First, the task was split from a multi-file sweep to single-file, surgical edits to reduce cognitive load. Second, the instruction was shifted from an action-oriented command ("insert templates") to a declarative state-oriented requirement ("ensure templates exist"). The instruction set restricted editing degrees of freedom to a surgical and minimal-edit contract.
```yaml
# The Final Surgical Edit Spec (11:39 AM)
execution_context:
  load:
    - "[MODULE_DIR]/[ANCHOR_SUBMODULE]/[CONTEXT_FILE].py"
  modify:
    - "[MODULE_DIR]/[ANCHOR_SUBMODULE]/[CONTEXT_FILE].py"

transformation_spec:
  operation: ensure_conformance  # Idempotent: No-op if state is already met

  normalize:
    sections:
      - name: "[SPECIALISTS]"
        anchor: "before line: [DECORATOR_A]"
        literal_template: |
          # ==============================================================================
          # [SPECIALISTS]
          # ==============================================================================
      - name: "[OBJECTIVES]"
        anchor: "before line: [DECORATOR_B]"
        literal_template: |
          # ==============================================================================
          # [OBJECTIVES]
          # ==============================================================================

  constraints:
    minimal_edit: true
    preserve_all_lines_outside_anchors: true

  prohibited:
    - import_modifications
    - method_body_modifications
    - line_deletion_outside_anchors

  verify:
    file_diff: "only contains the addition of the two literal templates"
```
**The Final Outcome:** 100% Success. Because formatting was framed as satisfying a strict structural contract (minimal edit / conformant no-op) rather than an open-ended code generation task, the editing model completed the work.
***

## FAQ
 
I'm working on it. Thanks for your patience! Reach out if you have questions.
## Status
 
✅ Stable. :-)

Will continue sporadic development on demand, add more patterns if trial and error reveals their utility, or consider including improvements by request.
 
 
## Contributing
 
Feel free to reach out if interested in contributing or collaborating! I can't promise that I will be available, and I will leave this closed for contribution officially until this gets more use. I'm happy to figure out how to feature others' expansions if people want to share them. 
 
 
## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
