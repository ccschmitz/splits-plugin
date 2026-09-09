# Splits plugin development

This repository packages runner workflows for Codex. The installable source is `plugins/splits`; `.agents/plugins/marketplace.json` exposes it through the `splits-plugins` marketplace.

- Keep the existing plugin identifier `splits` and its canonical workout-tree contract.
- Treat live Splits tool schemas as authoritative. Do not invent supported tools or capabilities.
- Keep provider credentials, user data, exports, and signed URLs out of this repository.
- Preserve the runner's requested scope. Reading or reviewing training is not permission to edit it.
- Keep network I/O, parsing, validation, and persistence in the Splits server where appropriate. This repository's skills coordinate the existing tools.
- Use the plugin-creator and skill-creator workflows when editing packaging or skills. Validate the plugin and every changed skill before handing off.
- Edit source files, never `~/.codex/plugins/cache`.
- Keep releases explicit: the current three workflows do not include automatic COROS run syncing or watch-workout delivery.

The Linear **Splits** project is the canonical execution backlog. The connected-MCP run-sync proposal is CHR-121. Keep implementation decisions there; do not create a competing roadmap in this repository. The main application repository is `ccschmitz/splits`.
