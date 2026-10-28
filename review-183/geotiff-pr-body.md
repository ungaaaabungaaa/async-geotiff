`RasterArray` currently uses the dataclass representation, which prints all raster/mask values and a private parent object. A small array can produce a long debug message, and a larger array depends on NumPy truncation settings.

Add a compact representation with the existing namespace style, shape in `(bands, height, width)` order, dtype, and explicit-mask presence:

```text
async_geotiff.RasterArray(shape=(3, 3, 4), dtype='uint8', has_mask=True)
```

Three fixture-based regressions cover uint8 RGB with and without an explicit mask, and a one-band float32 raster. Each assertion runs with NumPy's print threshold set to infinity. Raster operations and stored fields are unchanged.

Related to #53. This covers `RasterArray`; `GeoTIFF` and `Overview` representations remain separate work.

Validation on macOS arm64 / CPython 3.14.7:

- Before implementation: all three new representation assertions failed against the dataclass output.
- Full test suite: 514 passed, 4 skipped, 9 expected failures; 98 existing warnings.
- Strict MkDocs build, Ruff lint, and source/test formatting passed.
- Source distribution and wheel build passed; Git whitespace check passed.
- The previous head passed the upstream Python 3.11–3.14 CI matrix. The review follow-up requires a new upstream run.

An extra repository-wide Ruff format check flags two unchanged Markdown snippets (`dev-docs/specs/2026-10-26-geokey-crs-conformance-design.md` and `docs/blog/posts/introducing-async-geotiff.md`). The current CI requires Ruff lint and strict docs; both pass. No unrelated formatting changes are included.

Thanks to @kylebarron for issue #53 and to the existing developmentseed/geotiff-test-data fixture corpus. Codex assisted with implementation, tests, validation, and review. Existing licensing and fixture attribution are preserved.

Review follow-up: quote the dtype string and remove the API-page paragraph, as requested by @kylebarron. The three updated assertions failed before the formatter change; the full suite and the local quality checks above passed again after it. This changes only dtype formatting, not the constructor or array data.

The image below captures the original pre-review output; the current representation quotes the dtype as shown above.

![Captured output before and after the change](https://raw.githubusercontent.com/ungaaaabungaaa/async-geotiff/1810219b9204752713a58d6cfd3aa7b2b6aff518/contribution-evidence/53/validation-report.png)
