---
name: register-component
description: Registers a new component under an existing app in a data center's app inventory (creates its {component_name}.yml under the app's _apps/ subfolder). Use when the user asks to register a component, e.g. "register a component for proxmox", "add a component to drjr-homelab-01".
user-invocable: true
---

Walk through these steps to register a new component. Do not skip steps or assume values — if you are not sure of a value, ask the user. Follow the rules recorded in the owning data center's ADR (e.g. `drjr-adr/_drjr-homelab-01/0003-application-inventory.md`):

- Components must be registered before they are deployed.
- Component `name`s must be unique within their parent app (not globally).
- A component's `unique_id` is not numeric — it's `<app_unique_id>-<component_name>`.

1. **Identify the data center.** If the user named one (e.g. "drjr-homelab-01"), use it. Otherwise list the top-level `_*` folders in this repo and ask via AskUserQuestion which data center this component's app belongs to. Apps live under `{data-center-folder}/_apps/`.

2. **Identify the parent app.** List the subfolders under `{data-center-folder}/_apps/` (each is `{app_unique_id}-{app_name}`) and ask via AskUserQuestion which app this component belongs to, unless the user already named it unambiguously. Read that app's `.metadata.yml` to confirm its `unique_id`.

3. **Get the component name.** Ask the user for the component's `name` (short, lowercase — e.g. `lifecycle`, `token_rotation`). List every existing `{component_name}.yml` file directly under the chosen app's folder (excluding `.metadata.yml`) and check for a collision. If the proposed name is already taken by a component under this app, tell the user and ask for a different name — do not proceed until it's unique within this app. The same name is allowed to exist under a different app.

4. **Get the description.** Ask the user for a short description of what this component does within the app.

5. **Get the git repo.** Ask the user for the git repo that implements this component, in `Owner/repo-name` form.

6. **Determine the unique_id.** Combine the parent app's `unique_id` (from step 2) and the component `name` (from step 3): `{app_unique_id}-{name}`.

7. **Write `{name}.yml`** inside the parent app's folder with this exact structure:

   ```yaml
   unique_id: {unique_id from step 6}
   app_id: "{app_unique_id from step 2}"
   name: {name from step 3}
   description: {description from step 4}
   git_repo: {git_repo from step 5}
   ```

8. **Confirm** the created file path back to the user, and remind them the component must be registered before it's deployed — if it's already running, flag that this registration is happening after the fact.
