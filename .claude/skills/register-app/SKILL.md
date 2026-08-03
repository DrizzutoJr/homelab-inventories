---
name: register-app
description: Registers a new application in a data center's app inventory (creates its .metadata.yml under _apps/). Use when the user asks to register an app for a data center, e.g. "register this app for drjr_hml_01", before it is deployed.
user-invocable: true
---

Walk through these steps to register a new app. Do not skip steps or assume values — if you are not sure of a value, ask the user. Follow the rules recorded in the owning data center's ADR (e.g. `drjr-adr/_drjr_hml_01/0003-application-inventory.md`):

- All new apps must be registered before they are deployed.
- An app's `unique_id` is the next available number (zero-padded to 4 digits), matching its subfolder prefix.
- `name` must be unique across all apps already registered for that data center.
- `full_name` does not need to be unique.

1. **Identify the data center.** If the user named one (e.g. "drjr_hml_01"), use it. Otherwise list the top-level `_*` folders in this repo and ask via AskUserQuestion which data center this app belongs to. The apps live under `{data-center-folder}/_apps/`.

2. **Get the app name.** Ask the user for the app's `name` (short, lowercase, used as the unique identifier — e.g. `jenkins`). Read every existing `.metadata.yml` under `{data-center-folder}/_apps/*/.metadata.yml` and check its `name` field. If the proposed name is already taken, tell the user which app is using it and ask for a different name — do not proceed until it's unique.

3. **Get the full name.** Ask the user for the app's `full_name` (the human-readable/display name, e.g. `Jenkins`). This does not need to be unique.

4. **Get the link.** Ask the user for the app's official homepage or docs URL.

5. **Determine the unique_id.** List existing subfolders under `{data-center-folder}/_apps/` matching `^\d{4}-`, find the highest numeric prefix, and increment it (zero-padded to 4 digits). Start at `0001` if none exist.

6. **Determine the folder name.** Slugify the `name` from step 2 (lowercase, spaces/punctuation to hyphens). Final folder: `{unique_id}-{slug}` inside `{data-center-folder}/_apps/`.

7. **Set the registration date.** Use today's date in `YYYY-MM-DD` format.

8. **Write `.metadata.yml`** inside the new folder with this exact structure:

   ```yaml
   unique_id: "{unique_id}"
   name: {name from step 2}
   full_name: {full_name from step 3}
   link: {link from step 4}
   date_registered: {date from step 7}
   ```

9. **Confirm** the created file path back to the user, and remind them the app must be registered before it's deployed — if it's already running, flag that this registration is happening after the fact.
