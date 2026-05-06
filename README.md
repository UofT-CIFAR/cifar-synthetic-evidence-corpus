# CIFAR Synthetic Evidence Corpus

A benchmark for detecting AI-manipulated documentary evidence, comprising 2,490 documentary artifacts spanning three documentary-evidence families (receipts, emails, and business and administrative documents), four manipulation sophistication tiers, and four generator families.

The corpus is structured around three commitments that prior document-forgery datasets do not jointly satisfy: **source-disjoint training and test pools**, so generalisation gaps reflect substrate shift rather than item-level memorisation; **ablation-ready shortcut controls** recorded in the manifest so evaluators can directly measure each control's contribution; and **redundant provenance markers** on every artifact so that no corpus item can be mistaken for genuine evidence if it escapes the research environment.

## Composition

|        | T1  | T2  | T3  | T4  | Clean (T0) | Total |
|--------|----:|----:|----:|----:|-----------:|------:|
| **Training pool** (per family, per variant) | 20 | 20 | 20 | 20 | 250 | 570 |
| **Test pool** (per family, per variant)     | 10 | 10 | 10 | 10 | 100 | 240 |

Three families × {training: 570, test: 240} = 2,490 items total. Manipulated items are generated through the fully crossed family × tier × variant matrix described in the paper.

## Download

Each release of the corpus is published as an attached archive on the [Releases page](https://github.com/UofT-CIFAR/cifar-synthetic-evidence-corpus/releases). The current release is **v0.1.0**.

```bash
# Download the corpus archive (Release v0.1.0)
wget https://github.com/UofT-CIFAR/cifar-synthetic-evidence-corpus/releases/download/v0.1.0/corpus-v0.1.0.tar.gz

# Verify the checksum
wget https://github.com/UofT-CIFAR/cifar-synthetic-evidence-corpus/releases/download/v0.1.0/SHA256SUMS
sha256sum -c SHA256SUMS

# Extract
tar -xzf corpus-v0.1.0.tar.gz
```

The archive contains:

```
corpus-v0.1.0/
├── manifest.parquet     # one row per artifact; full ground truth
├── tools.yaml           # pinned generator versions
└── data/                # the artifacts themselves (PDF, PNG, JPG)
    ├── TRN-RCT-T1-A-item_001.pdf
    ├── TRN-RCT-T1-A-item_002.pdf
    └── ...
```

Filenames follow the convention `<POOL>-<FAMILY>-<TIER>-<VARIANT>-item_<NNN>.<ext>`, where `POOL` is `TRN` or `TST`, `FAMILY` is `RCT`/`EML`/`DOC`, `TIER` is `T0`–`T4`, and `VARIANT` is `A`–`D` (omitted for clean controls).

## Manifest

Every artifact corresponds to a single row in `manifest.parquet`. The full schema is documented in the Croissant metadata file (`croissant.json`), but the columns most users need are:

- `artifact_id`, `filename`, `sha256` — identifiers
- `pool`, `family`, `tier`, `variant` — classification
- `source_dataset`, `source_artifact_id`, `source_license` — substrate provenance (null for Tier 4)
- `tool_family`, `tool_version`, `prompt`, `edit_regions` — generation provenance
- `identity_seed`, `style_pool_index`, `letterhead_seed` — per-item seeds for ablation
- `intended_evidentiary_role` — free-text description
- `provenance_marker_confirmed` — boolean

The seeds support the ablation studies described in the paper. Regenerating the corpus with substituted identities, letterheads, fonts, or style pools while keeping other factors fixed allows direct attribution of detector performance to specific factors.

## Provenance markers

Every artifact, clean or manipulated, carries three redundant markers identifying it as corpus material: an EXIF/XMP tag with key `synthetic-evidence-corpus` and value `true` for images that retain metadata; a steganographic flag for images whose EXIF would be stripped by downstream processing; and a sentinel hash logged in the manifest. At least one marker is designed to survive a re-save through a consumer image editor. Markers are applied identically to clean and manipulated items, so they do not interfere with detector training.

## License

The corpus is released under [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/).

Substrate corpora retain their original licenses. Each item's source license is recorded per row in the manifest. Users redistributing or building on substrate-derived items must respect the original substrate licenses, including LDC's terms for Avocado-derived test-pool email items.

## Citation

```bibtex
@data{DVN/YY0IUH_2026,
  author    = {McConvey, Kelly and Mahdavimoghaddam, Jalehsadat and Ebrahimi, Sajad and Taranukhin, Maksym and Burkell, Jacquelyn and Deng, Yuntian and Eltis, Karen and Grossman, Maura and Lecuyer, Mathias and Shwartz, Vered and Bagheri, Ebrahim},
  title     = {{CIFAR Synthetic Evidence Corpus}},
  publisher = {GitHub},
  year      = {2026},
  version   = {v0.1.0},
  url       = {https://github.com/UofT-CIFAR/cifar-synthetic-evidence-corpus}
}
```

## Croissant metadata

A [Croissant](http://mlcommons.org/croissant/) JSON-LD metadata file (`croissant.json`) is included at the repository root, documenting the full manifest schema, distribution structure, and Responsible AI metadata.

## Limitations

The corpus covers documentary evidence only (no photographic, audio, or video evidence), three document families, one language (English), and a single manipulation per item. With the exception of 163 pre-labelled forgeries from Find It Again! retained as `tier=external_labeled` calibration material, every manipulated item is synthetic rather than drawn from real-world fraud. Detector performance on the corpus should not be read as evidence of deployment readiness for evidentiary use; the lab-to-wild gap and the difference between synthetic and real-world fraud are documented at length in the accompanying paper.

## Contact

For questions, issues, or contributions, please open an issue on this repository.
