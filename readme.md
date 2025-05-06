# HTMLHint Lint Workflow

This repository provides a reusable GitHub Actions workflow for linting HTML files with [HTMLHint](https://github.com/htmlhint/htmlhint) and reporting results to Moodle via an external assignment API. It can be easily imported into any project to automatically check HTML syntax based on custom or default rules.

## Features

- **Reusable Workflow**: Invoked via `workflow_call`, so you can call it from any other workflow.
- **Custom Configuration**: Automatically uses `.github/linters/.htmlhintrc` if present; otherwise falls back to default HTMLHint rules.
- **Full Repository Coverage**: Lints all `**/*.html` files in the repository (root and subdirectories).
- **Markdown Error Table**: Formats lint errors into a Markdown table for clear reporting.
- **Moodle Integration**: Posts the results (success/failure + detailed table) to a Moodle external assignment using `vars.MOODLE_URL` and the `secrets.MOODLE_TOKEN2`.
- **Optimizations**:
    - Concurrency control to cancel in-progress runs on the same branch.
    - Caching of `npm` modules to speed up repeated runs.
    - Permissions locked down to only necessary scopes.
    - Job-level timeouts to prevent hanging.

---

## How to Use

1. **Include in your workflow**:

   In your project, create a workflow YAML (e.g., `.github/workflows/lint-and-report.yml`) with:

   ```yaml
   name: Lint HTML and Report to Moodle

   on:
     push:
       branches: [main]
     pull_request:
       branches: [main]

   permissions:
     contents: read
     actions: write
     checks: write

   jobs:
     call-lint:
       uses: BZZ-Commons/htmlgrader/.github/workflows/workflow.yml@main
       with:
         html_glob: '**/*.html'     # optional override, default is '**/*.html'
       secrets: inherit            # uses org-level secrets for MOODLE_TOKEN2
