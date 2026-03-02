# exokern-eval

**Policy Report Card generator for contact-rich manipulation skills.**

Evaluate any policy checkpoint against standardized sim scenarios and get a publication-ready report with success rate, force profiles, and automatic comparison against [EXOKERN](https://huggingface.co/EXOKERN) baselines.

<p align="center">
  <img src="assets/report_dashboard.png" alt="EXOKERN Policy Report Card — HTML Dashboard" width="700">
</p>

## Quick Start

```bash
pip install exokern-eval

exokern-eval \
  --policy checkpoint.pt \
  --env Isaac-Forge-PegInsert-Direct-v0 \
  --episodes 100 \
  --output report.html
```

<p align="center">
  <img src="assets/terminal_output.png" alt="exokern-eval terminal output" width="600">
</p>

This produces an HTML report card with:
- Success rate with 95% confidence interval
- Average and peak contact force
- Completion time
- Automatic comparison against the EXOKERN baseline (if available)
- Grade badges (Excellent / Good / Needs Work)

## Installation

**Basic (checkpoint metadata only):**
```bash
pip install exokern-eval
```

**With Isaac Lab (full sim rollouts):**
```bash
pip install exokern-eval[isaaclab]
```

**From source:**
```bash
git clone https://github.com/MichaelLud1AI/exokern_eval.git
cd exokern_eval
pip install -e ".[dev]"
```

## Usage

### Full evaluation (requires Isaac Lab + GPU)

```bash
exokern-eval \
  --policy /path/to/best_model.pt \
  --env Isaac-Forge-PegInsert-Direct-v0 \
  --episodes 200 \
  --output report.html
```

### Offline mode (checkpoint info only, no GPU needed)

```bash
exokern-eval \
  --policy checkpoint.pt \
  --offline \
  --output report.json
```

### JSON output for CI/CD

```bash
exokern-eval \
  --policy checkpoint.pt \
  --episodes 100 \
  --output results.json
```

### Custom baseline comparison

```bash
exokern-eval \
  --policy checkpoint.pt \
  --baseline my_baseline.json \
  --output report.html
```

## CLI Reference

| Flag | Default | Description |
|------|---------|-------------|
| `--policy` | (required) | Path to `.pt` checkpoint |
| `--env` | `Isaac-Forge-PegInsert-Direct-v0` | Gymnasium environment name |
| `--episodes` | `100` | Number of rollout episodes |
| `--output` | `report.html` | Output path (`.html` or `.json`) |
| `--baseline` | `auto` | `auto` (built-in EXOKERN), path to JSON, or `none` |
| `--condition` | auto-detect | Override condition label (`full_ft` / `no_ft`) |
| `--offline` | `false` | Skip sim, report checkpoint metadata only |

## Built-in Baselines

exokern-eval ships with EXOKERN's validated baselines. When `--baseline auto` (the default), your policy is automatically compared:

| Environment | Condition | Success Rate | Avg Force | Source |
|-------------|-----------|:---:|:---:|--------|
| PegInsert | full_ft | 100% | 3.2 N | [EXOKERN/skill-forge-peginsert-v0](https://huggingface.co/EXOKERN/skill-forge-peginsert-v0) |
| PegInsert | no_ft | 100% | 5.2 N | [EXOKERN/skill-forge-peginsert-v0](https://huggingface.co/EXOKERN/skill-forge-peginsert-v0) |

## Example Output

See the screenshots above for terminal and HTML output. The HTML report is a single self-contained file -- no server needed, just open it in a browser.

## How It Works

1. **Load** a trained Diffusion Policy checkpoint (`.pt` file with model weights + normalization stats)
2. **Create** an Isaac Lab environment (headless)
3. **Run** N rollouts with DDIM sampling and action chunking
4. **Collect** success, force, and timing metrics per episode
5. **Compare** against built-in EXOKERN baselines
6. **Generate** an HTML or JSON report

## Supported Environments

Currently tested with NVIDIA Isaac Lab FORGE environments:

- `Isaac-Forge-PegInsert-Direct-v0`

More environments will be added as the [EXOKERN Skill Catalog](https://huggingface.co/EXOKERN) grows.

## License

Apache 2.0

## Links

- [EXOKERN on HuggingFace](https://huggingface.co/EXOKERN)
- [EXOKERN Skill: Peg Insert v0](https://huggingface.co/EXOKERN/skill-forge-peginsert-v0)
- [EXOKERN Dataset: ContactBench v0](https://huggingface.co/datasets/EXOKERN/contactbench-forge-peginsert-v0)
