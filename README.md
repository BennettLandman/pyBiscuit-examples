# pyBiscuit-examples

Reference data for [pyBiscuit](https://github.com/BennettLandman/pyBiscuit):
three subjects with BISCUIT's FreeSurfer-route inputs and the **real outputs of
`biscuit_FC_v2.2.sif`** (SHA-256
`e70aea6290ca3312a290618a8092b8992444bc47a7d3b42680109dfd714d4329`). pyBiscuit
uses this repo as an optional submodule at `tests/data/open-examples`, and its
reference-marked tests compare each ported stage against these outputs.

## Subjects and provenance

| Subject | Source | FreeSurfer | BISCUIT run |
|---|---|---|---|
| 0050952 | ABIDE I, NYU | 7.4.1 (`freesurfer-linux-ubuntu22_x86_64-7.4.1-20230614-7eb8460`) | `--surf_recon freesurfer`, template `mb101`, protocols `braincolor,dkt31`, 2025-09-17 |
| 0050961 | ABIDE I, NYU | 7.4.1 | same |
| 0050971 | ABIDE I, NYU | 7.4.1 | same |

Added by the owner (Bennett Landman) on 2026-10-22 and approved for git.
ABIDE data are open, with their own data-use and licence terms (ABIDE I is
distributed under a Creative Commons non-commercial share-alike licence;
check the current ABIDE terms before any redistribution beyond this
private repository).

## Layout (per subject)

| Path | Content | Role |
|---|---|---|
| `surf/{lh,rh}.{white,pial,sphere,inflated}` | FreeSurfer surfaces | BISCUIT input |
| `surf/{lh,rh}.{curv,sulc,inflated.H,thickness}` | FreeSurfer curv files | BISCUIT input |
| `label/{lh,rh}.cortex.label` | cortex mask | BISCUIT input |
| `surf/{lh,rh}.{white,gray,mid,inflated,sphere}.vtk` | surfaces as BISCUIT wrote them | image output |
| `reg/` | HSD coefficients and registered surfaces (`mb101`) | image output |
| `curve/` | TRACE sulcal/gyral curves (`.scurve`, `.gcurve`, `.bary`) | image output |
| `quant/` | LGI, sulcal depth, mean curvature, shape index, SCI, area, thickness | image output |
| `roi/` | cortex masks and parcellations (`braincolor`, `dkt31`) | image output |
| `stat/` | measures on the template and `stats.{braincolor,dkt31}.csv` | image output |
| `pdf/` | the five QA pages | image output |
| `scripts/build-stamp.txt`, `scripts/recon-all.done` | FreeSurfer provenance | metadata |

Not included: FreeSurfer volumes (`mri/`), other FreeSurfer surfaces, labels
and stats, and BISCUIT intermediates (the run kept none; `tmp/` was empty).

## Format

Files of 4 KB or more are stored gzip-compressed (`<name>.gz`, deterministic:
`mtime=0`, level 6), except PDFs. `MANIFEST.sha256` lists the SHA-256 of every
**original, uncompressed** file; pyBiscuit's loaders read `.gz` copies
transparently. To check integrity:

```bash
python - <<'PY'
import gzip, hashlib, pathlib
for line in open("MANIFEST.sha256"):
    digest, rel = line.split(maxsplit=1); rel = rel.strip(); p = pathlib.Path(rel)
    data = p.read_bytes() if p.exists() else gzip.decompress(pathlib.Path(rel + ".gz").read_bytes())
    assert hashlib.sha256(data).hexdigest() == digest, rel
print("ok")
PY
```
