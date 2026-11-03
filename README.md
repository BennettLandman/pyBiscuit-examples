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

Added by Bennett Landman on 2026-10-22.

## License and attribution

The source images come from the **Autism Brain Imaging Data Exchange (ABIDE I)**,
NYU Langone Medical Center site. ABIDE I shares them through the 1000 Functional
Connectomes Project / INDI under the
[Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported](https://creativecommons.org/licenses/by-nc-sa/3.0/)
license (CC BY-NC-SA 3.0), for non-commercial research only
([ABIDE I terms](https://fcon_1000.projects.nitrc.org/indi/abide/abide_I.html)).

Everything in this repository is a derivative of those images: the FreeSurfer
reconstructions and the BISCUIT outputs. It is therefore released under the
same license, **CC BY-NC-SA 3.0** (full text in [LICENSE](LICENSE)):

- **Attribution:** credit ABIDE I (NYU site) and this repository, and name the
  subjects you used (`0050952`, `0050961`, `0050971`).
- **NonCommercial:** no commercial use.
- **ShareAlike:** release derivatives of these files under the same license.

If you use these data, please cite:

> Di Martino A, Yan C-G, Li Q, et al. The autism brain imaging data exchange:
> towards a large-scale evaluation of the intrinsic brain architecture in autism.
> *Mol Psychiatry* 19, 659–667 (2014). https://doi.org/10.1038/mp.2013.78

Also acknowledge the ABIDE funding sources listed on the ABIDE I page. This
license covers the data only. The pyBiscuit software is Apache-2.0.

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

Redacted: the FreeSurfer username and hostname of the original run were
replaced with same-length placeholders (`xxxxx`, `xxxxxxxxx`) in the surface
headers and tags and in `scripts/recon-all.done`. Geometry is unchanged, and
`MANIFEST.sha256` lists the redacted files.

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
