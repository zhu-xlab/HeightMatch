# HeightMatch

This repository provides the basic pipeline for **HeightMatch** using **INRIA_Buildings** as an example.

## Workflow

The recommended order is:

1. Generate synthetic height maps
2. Train the model
3. Test the model

---

## 1. Generate synthetic height maps

Run `generate_synthetic_height.py` to generate synthetic height maps from RGB images.

Example for one city:

```bash
python3 generate_synthetic_height.py \
    --imgdir /home/Datasets/RSSeg/INRIA/Austin/image \
    --outdir /home/Datasets/RSSeg/INRIA/Austin/height
```

### Important note

The script only accepts one `imgdir` and one `outdir` at a time.  
Therefore, if the INRIA dataset is organized by multiple cities, you need to run the script once for each city.

Example:

```bash
python3 generate_synthetic_height.py \
    --imgdir /home/Datasets/RSSeg/INRIA/Austin/image \
    --outdir /home/Datasets/RSSeg/INRIA/Austin/height

python3 generate_synthetic_height.py \
    --imgdir /home/Datasets/RSSeg/INRIA/Chicago/image \
    --outdir /home/Datasets/RSSeg/INRIA/Chicago/height
```

The generated height maps are saved in the folder specified by `--outdir`.

---

## 2. Prepare split files

Before training, make sure the split files are ready, for example:

```text
splits/inria_buildings/
├── val.txt
├── test.txt
└── 0.5/
    ├── labeled.txt
    └── unlabeled.txt
```

Each line should contain:

```text
image_path label_path height_path
```

Example:

```text
Austin/image/austin1.tif Austin/label/austin1.tif Austin/height/austin1.png
```

The third path must point to the generated synthetic height map.

---

## 3. Train

Training is launched with `train.sh`.

Example settings in `train.sh`:

```bash
dataset='inria_buildings'
split='0.5'
method='heightmatch'
model='dinov2_small'
```

Run training:

```bash
bash train.sh 4
```

Here, `4` means using 4 GPUs.

The checkpoints are saved to:

```text
saved/inria_buildings/0.5/heightmatch/dinov2_small/
```

---

## 4. Test

Testing is launched with `test.sh`.

Make sure the settings in `test.sh` are consistent with training:

```bash
dataset='inria_buildings'
split='0.5'
method='heightmatch'
model='dinov2_small'
```

Run testing:

```bash
bash test.sh
```

The testing script loads:

```text
saved/inria_buildings/0.5/heightmatch/dinov2_small/best.pth
```

The results are written to:

```text
test_results.xlsx
```

---

## 5. Minimal example

```bash
# Step 1: generate synthetic height maps
python3 generate_synthetic_height.py \
    --imgdir /home/Datasets/RSSeg/INRIA/Austin/image \
    --outdir /home/Datasets/RSSeg/INRIA/Austin/height

# Repeat for other cities if needed

# Step 2: train
bash train.sh 4

# Step 3: test
bash test.sh
```
