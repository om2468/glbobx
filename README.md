# GLB to OBJ Batch Converter

This repository contains a tiny command-line tool that walks through an input
folder, finds every `.glb` model, and exports each as `.obj` (plus `.mtl`
materials) into a mirrored output folder.

## Features

- Recursive or flat directory scanning
- Preserves the input folder structure under the output directory
- Skips already converted files unless `--overwrite` is provided
- Helpful logging, including warnings when the input directory is empty

## Requirements

- Python 3.9+
- [pip](https://pip.pypa.io/) for dependency installation

Install dependencies:

```bash
pip install -r requirements.txt
```

## Usage

Choose whichever workflow fits best.

### Option 1 — CLI batch converter

By default the script looks for GLB files inside `./input` and writes OBJ files
into `./output`. The directories are created as needed.

```bash
python convert.py
```

### Option 2 — Streamlit web app

Launch the browser UI and convert files interactively:

```bash
streamlit run streamlit_app.py
```

Drag-and-drop a `.glb` file and immediately download the generated `.obj`
bundle as a ZIP archive. The sidebar recaps the steps and caveats.

#### Using the same API elsewhere

Behind the scenes the Streamlit app calls the shared `convert_glb_bytes`
helper. You can consume that function directly from Python (or plug it into
another framework) with this snippet:

```python
from pathlib import Path

from convert import convert_glb_bytes

payload = Path("example.glb").read_bytes()
archive_bytes, artefacts = convert_glb_bytes(payload, filename="example.glb")

output_path = Path("example.zip")
output_path.write_bytes(archive_bytes)
print("Generated:", artefacts)
```

To match the Streamlit experience:

1. Start the app with `streamlit run streamlit_app.py`.
2. Upload a GLB file via the uploader widget.
3. Streamlit delegates to `convert_glb_bytes`, displays the artefact list, and
   provides a download button.

Because the conversion lives in `convert_glb_bytes`, you can drop it into
FastAPI, Flask, serverless functions, or desktop tools without touching the CLI
wrapper.

### Command-line options

| Flag | Description |
| ---- | ----------- |
| `--input`, `-i` | Alternate directory containing `.glb` files (defaults to `./input`). |
| `--output`, `-o` | Directory to write `.obj` exports (defaults to `./output`). |
| `--recursive` | Search within subdirectories of the input folder. |
| `--overwrite` | Force regeneration of `.obj` files even if they already exist. |
| `--quiet`, `-q` | Suppress informational logging. |

Example converting files in a custom directory and forcing overwrites:

```bash
python convert.py --input assets/glb --output build/obj --recursive --overwrite
```

## Workflow

1. Place your `.glb` files into the `input/` directory (create subfolders as
   needed).
2. Run `python convert.py`.
3. Collect the generated `.obj` (and `.mtl`) files from the `output/`
   directory. The folder structure mirrors what you placed under `input/`.

## Notes

- The converter relies on [`trimesh`](https://trimsh.org/); conversion quality
  depends on what that library supports.
- When the input folder is missing, the script creates it and exits with a
  helpful warning so you can drop your models in before running again.
- Keep texture files alongside the original `.glb` if you want them referenced
  in the exported `.mtl` files.

## Deploying to Streamlit Community Cloud (free)

1. Push this repository to GitHub (public or private).
2. Sign in to [streamlit.io](https://streamlit.io/) and open **Community Cloud**.
3. Click **New app**, pick your repository, branch, and set the entry point to
   `streamlit_app.py` (the Streamlit UI in this repo).
4. Leave the default Python version (3.9/3.10+) and ensure `requirements.txt`
   is selected so dependencies (`trimesh`, `streamlit`, …) are installed.
5. Deploy the app. Each time you push to the selected branch, Streamlit Cloud
   rebuilds and redeploys automatically.

Tips:

- Free tier apps sleep when idle; the first request after a pause may take
  longer while the environment spins up.
- Streamlit provides up to 1 GB of storage per app. Heavy models can be large,
  so advise users to keep uploads reasonable.
