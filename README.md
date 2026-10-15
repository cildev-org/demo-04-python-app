# demo-04-python-app

Demo target for Blue-Oryx dependency pull requests (fix class A1).

> **This app is vulnerable on purpose.** `main` pins `aiohttp==3.8.5`, which
> is affected by CVE-2024-23334 (a static route served with
> `follow_symlinks=True` reads files outside its folder). Do not run it
> anywhere that is reachable from a network you care about.

## What is here

| File | Purpose |
| --- | --- |
| `requirements.txt` | The manifest. One direct dependency, pinned with `==` |
| `server.py` | aiohttp app with a static route that follows symlinks |
| `static/index.txt` | A file inside the static root, for the positive control of the re-run |
| `Dockerfile` | Python 3.11 image (base pinned by digest) that installs `requirements.txt` |
| `.github/workflows/build.yml` | Builds the image on every pull request; on a push to `main` it also pushes it to `ghcr.io/<repo>:<commit>` |

## Demo flow

1. Blue-Oryx receives a confirmed finding for CVE-2024-23334 on the asset bound
   to this repository.
2. It opens a pull request from a `blue-oryx/<id>` branch that changes one
   thing: the aiohttp pin in `requirements.txt`, to the lowest fixed version
   (`3.9.2`).
3. The `build` workflow runs on the pull request.
4. A person reviews and merges the pull request. Blue-Oryx never merges.
5. The workflow pushes the image of the merge commit. The operator deploys it
   to the lab target; Blue-Oryx never deploys.
6. The operator confirms the deploy and Blue-Oryx runs the same exploit again.
   Only a blocked re-run makes the task `verified-fixed`.

## Branches

| Branch | Purpose |
| --- | --- |
| `main` | What Blue-Oryx patches |
| `vulnerable-baseline` | The vulnerable state, for resetting `main` between demo runs |
