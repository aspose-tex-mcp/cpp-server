# TeX C++ server validation

## Registered GitHub components

- Engine: `https://github.com/aspose-core-mcp/context-engine.git`, commit `e917d8cc1dbff142ba67cc6b45e75c271c8daa16`.
- Plugin: `https://github.com/aspose-tex-mcp/cpp-plugin.git`, commit `afc5caa614a37a745d8d424dedd087b9b7f0aeec`.
- Parent remote: `https://github.com/aspose-tex-mcp/cpp-server.git`.

## Local verification

- Catalog registration, retrieval profile and isolated worker fixture extraction: passed.
- TeX plugin regression suite: 14 passed.
- TeX C++ endpoint suite: 43 passed.

Tests used an existing local Python environment against this checkout's source.
They do not prove a fresh installation, hosted GitHub CI success, Docker connectivity,
full-corpus ingestion, or persistent retrieval. The endpoint suite emitted one
non-failing Starlette/httpx deprecation warning.

## Publication

The parent repository's component references are staged. Commit the parent files
and references, then push to the configured remote after review. No component
source modifications or remote publication were performed during this setup.
Choose an appropriate root deployment license before public release; component
licenses remain in their respective submodules.
