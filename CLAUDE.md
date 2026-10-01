# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project status

This is the semester project for the BI-ML course (Ingeniería Industrial, Universidad de Concepción, 2026-1). The repository has not been set up yet: the only file is `Trabajo BIML 2026-1.pdf`, the assignment brief and grading rubric. There is no code, build system, test suite, or git repository yet. When those are added, update this file with the real commands and architecture.

Deliverables (README, report, presentation) are written in Spanish.

## What the assignment requires (from the PDF)

Build a complete, maintainable data-analysis pipeline that answers a concrete BI/business or research problem. The problem domain is agreed individually with the professors, and the dataset comes from their list of validated datasets.

The pipeline must be cumulative and multi-stage:
1. Define the problem and research question, plus a work plan.
2. ETL on the raw data.
3. Descriptive/exploratory analysis.
4. Formulate, train, and validate ML/DL models.
5. Deliver a prototype solution as a package of tools plus an associated workflow, and show that the pipeline can be maintained efficiently over time.

Required deliverables:
- A version-controlled repository covering both code **and data** (datasets are expected to be versioned too).
- A `README.md` with a **version history that records technical and methodological decisions**. GitHub issues are suggested as an alternative decision log.
- A document (Word, Markdown, or similar) showing how each objective was met.
- A presentation that includes a demo of the solution.

## Grading rubric (each item is worth up to 1 point)

Keep these in mind when writing docs or making design choices, since each one needs visible evidence in the report or repo:
- Report formatting: font size, spacing, and properly cited sources, applied consistently across the whole document.
- Pipeline and data handling: data management **plus** a description of the data **plus** justification of each decision. All three are needed for full marks.
- A complete description of both the problem and the proposed solution.
- A BI/BA architecture diagram that is complete and consistent with the proposed solution.
- A description of the potential users **and** how data risks are managed.
- A clear presentation that stays within the time limit and matches the report.

Technical decisions are supposed to be validated with the professors during development, so point out any decision that probably needs their sign-off instead of treating it as settled.
