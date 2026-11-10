# Aspose.TeX for C++ MCP Server

Deploy the shared Aspose context engine with the TeX C++ extraction plugin, then connect an MCP-compatible client to documentation and API tools. This repository supplies pinned components, a catalog, and setup, launch and ingestion helpers. It does not include indexed content or provision a hosted service.

## Project family

| Repository | Responsibility |
| --- | --- |
| [context-engine](https://github.com/aspose-core-mcp/context-engine) | Shared ingestion, retrieval and MCP infrastructure |
| [cpp-plugin](https://github.com/aspose-tex-mcp/cpp-plugin) | TeX C++ documentation/API extraction |
| [cpp-server](https://github.com/aspose-tex-mcp/cpp-server) | Deployment helpers and pinned components (this repository) |

## Get the server

```sh
git clone https://github.com/aspose-tex-mcp/cpp-server.git
cd cpp-server
```

Do not clone recursively. Setup initializes only the engine and TeX plugin, not historical engine source submodules. Preserve local submodule edits before setup.

## Setup and verification

Requires Git and Python 3.12+. From the server root:

```powershell
.\scripts\setup.ps1
```

Linux:

```sh
sh scripts/setup.sh
```

Setup installs the engine into `.venv`, creates a separate plugin interpreter and verifies registration and fixture extraction. It does not download documentation, prepare Gists or publish content.

## Run the server

Configure runtime stores, credentials and embeddings using the [engine README](engine/README.md). This checkout does not include the older standalone `.env.tex.example` or `docker-compose.tex-local.yml`; do not use instructions referring to those files. Select and configure the engine infrastructure for your environment before ingestion.

PowerShell:

```powershell
.\scripts\run-engine.ps1 -m uvicorn adcs.api.main:app --host 127.0.0.1 --port 8084
```

Linux:

```sh
sh scripts/run-engine.sh -m uvicorn adcs.api.main:app --host 127.0.0.1 --port 8084
```

For queued ingestion, start another process using the same wrapper with `-m celery -A adcs.worker.celery_app:celery_app worker --loglevel=info`. The wrappers select the TeX catalog and run from `engine/`. Use the same runtime configuration during ingestion and serving.

## Download sources

From the server root, use normal clones (only when the destinations do not exist):

```powershell
$env:GIT_LFS_SKIP_SMUDGE = '1'
New-Item -ItemType Directory -Force engine/sources | Out-Null
git clone --branch main https://github.com/aspose-tex-mcp/docs.git engine/sources/tex-documentation
if ($LASTEXITCODE -ne 0) { throw 'Documentation clone failed.' }
git clone --branch master https://github.com/aspose-tex-mcp/reference.git engine/sources/tex-api-reference
if ($LASTEXITCODE -ne 0) { throw 'API clone failed.' }
```

Linux:

```sh
export GIT_LFS_SKIP_SMUDGE=1
mkdir -p engine/sources
git clone --branch main https://github.com/aspose-tex-mcp/docs.git engine/sources/tex-documentation
git clone --branch master https://github.com/aspose-tex-mcp/reference.git engine/sources/tex-api-reference
```

Stop if a clone fails. The manifest selects only `en/cpp` documentation and `english/cpp` API references. LFS binary downloads are skipped. Source paths are relative to the engine workspace; plugin-local source checkouts are not used by these server wrappers.

## Prepare examples and ingest

Prepare the referenced Gist examples before validation. From the server root:

```powershell
$env:PYTHONPATH = Join-Path $PWD 'plugins/tex-cpp/src'
& .\plugins\tex-cpp\.venv\Scripts\python.exe -m adcs_tex_cpp.gist_examples --source-root engine/sources/tex-documentation/en/cpp --cache plugins/tex-cpp/sources/gist-examples.json --resolve-history
if ($LASTEXITCODE -ne 0) { throw 'Gist preparation failed.' }
.\scripts\ingest.ps1 --source-kind both --timeout 300
# Continue only after validation succeeds:
.\scripts\ingest.ps1 --source-kind docs --publish --timeout 300
.\scripts\ingest.ps1 --source-kind apiref --publish --timeout 300
```

Linux:

```sh
PYTHONPATH=plugins/tex-cpp/src plugins/tex-cpp/.venv/bin/python -m adcs_tex_cpp.gist_examples --source-root engine/sources/tex-documentation/en/cpp --cache plugins/tex-cpp/sources/gist-examples.json --resolve-history
sh scripts/ingest.sh --source-kind both --timeout 300
# Continue only after preparation and validation succeed:
sh scripts/ingest.sh --source-kind docs --publish --timeout 300
sh scripts/ingest.sh --source-kind apiref --publish --timeout 300
```

Stop on failures. These commands target TeX C++ only. Validation does not publish; `--publish` requires configured backing services. Do not run overlapping ingestion or modify sources during a run.

## Connect a client

```text
http://127.0.0.1:8084/tex/cpp/mcp/v1/streamable-http
```

Initialization reports `aspose-tex-cpp-mcp`. For remote clients, use your reachable HTTPS deployment or a tunnel; localhost refers to the client's machine. Registration and initialization do not prove ingestion or retrieval quality.

## Scope and files

Only `aspose.tex.cpp` is registered, with no Total composition. The inherited engine still defines other product routes; this deployment does not remove them.

| Path | Purpose |
| --- | --- |
| `engine/` | Shared engine submodule |
| `plugins/tex-cpp/` | TeX C++ plugin submodule |
| `config/product-platform-plugins.json` | TeX-only registration catalog |
| `scripts/setup.*` | Component initialization and registration checks |
| `scripts/run-engine.*` | Engine launcher with deployment catalogs |
| `scripts/ingest.*` | TeX ingestion wrappers |
| `scripts/prepare-sources.py` | Optional manifest-driven clone helper; does not prepare Gists |
| `VALIDATION.md` | Verification scope and component revisions |

Publish component commits before updating parent references. A parent push does not upload local edits inside submodules.

## Validation and licenses

GitHub Actions checks shell syntax and the catalog, then runs registration, TeX C++ MCP endpoint tests, and the plugin regression suite. It does not deploy services or publish content.

Engine and plugin license texts and notices are supplied by their respective pinned repositories. Preserve them when distributing those components. This repository is licensed under [MIT](LICENSE), copyright (c) 2001 - 2026 Aspose Pty Ltd.
