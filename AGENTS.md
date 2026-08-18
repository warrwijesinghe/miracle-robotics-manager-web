# Miracle Robotics Manager — Continuation Instructions

## Read this first

Every Codex agent working in this workspace must read this file completely before inspecting, editing, running, or creating project files. Follow these instructions for every task unless the user explicitly overrides a specific instruction.

## Repository scope

This workspace contains three separate existing Git repositories:

- `miracle-robotics-manager-admin` — React/Vite/Tailwind administration frontend.
- `miracle-robotics-manager-api` — Node.js/Express/Sequelize/MariaDB API.
- `miracle-robotics-manager-web` — public website.

Work only in the repositories the user names. The public website repository must remain untouched unless the user explicitly asks to work on it.

Do not create repositories, reinitialize Git, alter remotes, push, create pull requests, or make commits unless explicitly requested.

## Architecture and data rules

- Admin frontend consumes the API through `VITE_API_URL`; never put database credentials in the frontend.
- API routes use `/api/...`; maintain consistent `{ success, data }` success responses and `{ success: false, message }` error responses.
- MariaDB schema changes require Sequelize migrations. Never reset, recreate, truncate, or drop the database or unrelated data.
- Never use destructive Sequelize synchronization (`sync({ force: true })` or `sync({ alter: true })`).
- Use DB transactions for registration numbers, student creation/conversion, stage changes, class reassignment, attendance saving, and payment upserts.
- Student registration numbers come only from the backend service. Physical students use `MRP-YYMM-XXXX`, online students `MRO-YYMM-XXXX`, and centre students `MRC-YYMM-XXXX`.
- Pending registrations must never consume registration numbers.
- Keep student learning, toolkit, certificate, and class-assignment history; do not overwrite or delete historical records.
- Prefer safe deactivation over physical deletion for master data and records with dependencies.

## Current course terminology

Use this hierarchy consistently:

```text
Course Type → Course Series → Stages
Robotics    → Junior Robotics → Stage 1, Stage 2, Stage 3
```

- Current Course Type: `Robotics`.
- Foundation Robotics, Kids Robotics, Junior Robotics, Smart Robotics, Future Robotics, and Global Robotics are Course Series.
- `Stage 1`, `Stage 2`, and `Stage 3` remain stages, not courses.
- Display Course Title as a generated value, for example `Junior Robotics Stage 01`.

## Admin module behavior

Main sidebar modules should open a module home/dashboard panel first. The home panel should provide operational shortcuts and a concise module-specific dashboard. The actual data-management list is accessed through an explicit shortcut.

## Master-file UI/UX standard

Every master file must use this same structure:

1. Page header with master-file label, title, explanation, and primary Add button.
2. Filters at the top of the data list, including search and Active/Inactive status.
3. Default to Active records.
4. Paginated data table on the left.
5. Pagination controls: First, Previous, nearby page numbers, Next, and Last; show the record range/count.
6. Persistent right-side data panel on desktop (fixed 360px width); never use a full-screen overlay for this panel.
7. On smaller screens only, stack the data panel below the table.
8. Clicking a row loads its data into the right-side panel.
9. Create, view, edit, activate/deactivate, and safe-delete behavior must be available in that same data panel. Use deactivate rather than destructive deletion when history/dependencies exist.
10. Include useful loading, empty, error, saving, and disabled-submit states.

Current Course Type Master File already follows this pattern. Course Series and Course Stage master files should be completed using the same standard.

## Student directory standard

- Default to active students.
- Use a paginated table with First/Previous/page-number/Next/Last controls.
- Keep a persistent right-side Student Data panel.
- Selecting a student loads editable profile fields in the panel.
- Preserve a link to the full student profile for learning history, class assignment, toolkit, certificate, and payments.

## Quality and verification

- Do not treat a production build as UI verification by itself.
- Guard array/optional relationship rendering; show `—` for missing optional data.
- Never return a blank/white screen. Keep the React Error Boundary active.
- Do not pass an async loader directly to `useEffect`; invoke it inside an effect callback so the effect does not return a Promise.
- Run `npm run build` in the admin repository after frontend changes.
- Run relevant API endpoint checks and migration status after backend/database changes.
- Do not claim a workflow works unless it was actually verified.

## Imported certificate students

- Historical certificate import script: `miracle-robotics-manager-api/scripts/import-certificate-students.js`.
- Command: `npm run import:certificates`.
- Imported records are Physical (`MRP`) students, ordered ascending by certificate completion date before registration number generation.
- Missing DOB/guardian contact information is intentionally blank for later completion; imported gender is currently Male as instructed.
