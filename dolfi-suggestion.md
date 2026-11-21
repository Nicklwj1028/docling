**Dolfim-ibm** (review submitted 2026-11-18) requested changes on PR #5402. The review has two blockers and several smaller points.

**Blocker 1: keep the old names working**

- On commit `f611830b`, `from_preset("granite_vision")` raises `KeyError` for both VLM convert and picture description.
- This breaks the CLI `--vlm-model granite_vision`, the docling-serve `*_preset` fields, and SDK code.
- These names were removed and must be restored as aliases:
  - `granite_picture_description`
  - `GRANITE_VISION_MODEL_SPEC_BASE`
  - `VLM_CONVERT_GRANITE_VISION`
  - `PICTURE_DESC_GRANITE_VISION`
- Add a test for `from_preset("granite_vision")`.
- He discusses two alias targets:
  - **Option A (PeterStaar's proposal):** point `granite_vision` to v3.3. This changes nothing for current users.
  - **Option B:** point `granite_vision` to v4. This matches chart extraction, where the legacy `"granite-vision"` maps to `granite_vision_v4`. It is a behavior change, so the release notes must state it.

**Blocker 2: `granite_vision_4_1_picture_description` fails on transformers < 5.8**

- The legacy `PictureDescriptionVlmModel` downloads `main` and loads without `trust_remote_code`.
- The native `granite4_vision` class exists only from transformers 5.8.
- `pyproject.toml` still allows `transformers>=4.42` on non-macOS.
- The notebook and `enrichments.md` point users to this option.
- Suggested fix: remove it. Point the docs to `PictureDescriptionVlmEngineOptions.from_preset("granite_vision_v4")`, which pins the revision and handles remote code.

**Smaller points**

- `enrichments.md:154` still links to the 3.1-2b model card.
- `docling-tools models download granite_vision` fetches only 3.3-2B.
- The chart v4 preset sets `torch_dtype="bfloat16"`, but the new v4 presets do not. He asks whether this is intended.
- The `API_OPENAI` model name differs from the chart v4 preset (`ibm-granite/granite-vision-4.1-4b` vs `granite-vision-4.1-4b`).
- The PR description still describes an in-place upgrade. It needs an update.

**Related review comments**

- **PeterStaar-IBM** (CHANGES_REQUESTED) asked for explicit names such as `GRANITE_VISION_3_3_2B` and `GRANITE_VISION_4_1_4B`. He also asked to keep the `granite-vision` shortcut and check support in transformers, MLX-VLM, and the OpenAI API.
- **github-actions bot** flagged the same `trust_remote_code` and unpinned `main` revision issue in the inline review on `stage_model_specs.py`.

The contributor, Yashas-naidu, replied on 2026-11-19 that they will address these points.