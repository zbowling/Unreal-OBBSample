# Agent Instructions — Unreal OBB Sample

Unreal Engine 5+ sample showing how to split a large project across multiple OBB expansion files (required + optional / DLC) and upload them to a Meta Horizon release channel.

## Source-of-truth files (read these first, do not duplicate their contents in this file)

For setup, build steps, SDK versions, and project layout, read:

- `README.md` — full walkthrough of chunks, paks, OBBs, and the upload pipeline
- `OBBSample.uproject` — Unreal engine version, plugins, and modules
- `Config/` — Unreal project config (chunk / asset manager rules)
- `config.bat`, `setup.bat`, `package_project_for_upload.bat`, `upload_package.bat` — Windows build / upload pipeline scripts
- `LICENSE` — Meta License (MIT applies only to specifically marked files)

## Quest / Horizon-specific notes

- The pipeline is **Windows-only by design** — `setup.bat`, `package_project_for_upload.bat`, `upload_package.bat`, and `config.bat` are batch files invoked from a Windows command prompt. There is no macOS or Linux equivalent.
- Hard packaging limits this sample exists to work around: APKs cap at 1 GB, OBBs cap at 4 GB, one designated "main" OBB plus unlimited "patch" / additional OBBs. Stay within these or store submission will fail.
- `setup.bat` deliberately generates large dummy files so the project crosses the 1 GB APK threshold and forces real multi-OBB packaging — running it the first time can take a long time while shaders compile, and the script is safe to kill and re-run if it appears to hang.
- Each upload requires bumping **Project Settings > Platforms > Android > Store Version** monotonically; the store rejects uploads that reuse a version code.
- Chunks 100 and 200 are picked arbitrarily by this sample to demonstrate the concept — the chunk IDs themselves carry no semantic meaning. Do not collapse them back into Chunk 0 when refactoring, or the OBB-split demonstration is lost.
- VRC checks `VRC.Quest.Packaging.5` and `VRC.Quest.Packaging.6` document the upstream APK/OBB constraints this sample is built around.

## Meta Quest tooling

This repository is part of the Meta Quest / Horizon OS ecosystem (a sample, library, template, or related project — the bespoke intro above describes which). Use that intro and the source-of-truth files it references for project-specific decisions; don't restate or invent facts from memory.

When the user asks anything about Quest device behavior, build / deploy / debug / capture flows, on-device performance, or Horizon OS APIs, reach for these tools instead of generic Unreal answers:

- **`hzdb`** — Quest-aware ADB wrapper (device list, install / launch / stop, logs, screenshots, Perfetto traces, on-device docs search). Already wired up as an MCP server via `.mcp.json`, `.vscode/mcp.json`, and `.cursor/mcp.json`. Also runnable directly: `npx -y @meta-quest/hzdb <subcommand>`.
- **Meta Quest Agentic Tools** — the full skill set, including Unreal-specific skills: [github.com/meta-quest/agentic-tools](https://github.com/meta-quest/agentic-tools). Install per your client (Claude Code: `/plugin install meta-vr@meta-quest`; Gemini CLI: `gemini extensions install https://github.com/meta-quest/agentic-tools`; Cursor / VS Code: install the **Meta Horizon** extension from the Marketplace).

A few behavior expectations:

- **Read this repo's files first.** Before answering anything project-specific, read `README.md` and whichever source-of-truth files the intro above points at. Don't restate their contents in chat — quote or link instead.
- **Use `hzdb` for device-side work.** Anything that touches an attached Quest (install, launch, logs, screenshot, capture, manifest inspection) goes through `hzdb`, not raw `adb`.
- **Check live Horizon OS docs before answering API questions.** `hzdb docs search "..."` queries the live docs; training data on Horizon OS APIs goes stale fast.
- **Don't fabricate SDK / engine versions.** If a version isn't visible in this repo's files, say so rather than guessing.
