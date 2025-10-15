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

By default the script looks for GLB files inside `./input` and writes OBJ files
into `./output`. The directories are created as needed.

```bash
python convert.py
```

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
