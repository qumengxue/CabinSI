# CabinSI Benchmark
This is the official repository for the ECCV 2026 accepted paper 'CabinSI: Omni-Cabin Spatial Reasoning through Explicit Visual Cognitive Maps.'

## Dataset Download

### Image Download

Due to the large size of image files, please apply for download through ModelScope:

🔗 **Image Download URL**: https://www.modelscope.cn/datasets/shimmerrr/CabinSI

**Download Steps:**
1. Visit the link above
2. Click the "Request Download" button
3. Wait for approval
4. Download the image files

### File Organization After Download

After downloading, please place the image files in the `images/` directory, maintaining the original directory structure:

```
CabinSI/
├── images/                           # Store downloaded images here
│   ├── CabinSI/
│   │   ├── CabinSI_final_version/
│   │   │   ├── filtered_inside_member_boxed/
│   │   │   │   ├── filtered_inside_0/
│   │   │   │   │   ├── inside_1.jpg
│   │   │   │   │   ├── inside_2.jpg
│   │   │   │   │   └── inside_3.jpg
│   │   │   │   └── ...
│   │   │   └── ...
│   │   ├── cross_cabin/
│   │   │   ├── samples/
│   │   │   │   └── ...
│   │   │   └── ...
│   │   └── out_cabin/
│   │       └── ...
│   └── ...
├── json_file/                        # JSON annotation files
│   ├── CabinSI_RelCabin_incabin_multiview.json
│   ├── CabinSI_RelCabin_outcabin_singleview__Ego3D.json
│   ├── CabinSI_RefCabin_incabin_multiview.json
│   ├── CabinSI_RefCabin_incabin_multiview_all_wo_member_box.json
│   ├── CabinSI_RefCabin_crosscabin_multiview_w_member_id.json
│   └── CabinSI_RefCabin_crosscabin_multiview_vague_modified_w_member_id.json
└── README.md                         # This file
```

## Data Preprocessing

### Replace [ROOT_PATH]

The image paths in JSON files use the `[ROOT_PATH]` placeholder, which needs to be replaced with the actual path before use.

<!-- #### Method 1: Batch Replacement Using Python Script

```python
import json
import os
import glob

# Configuration
json_dir = "json_file"
old_root = "[ROOT_PATH]"
new_root = "."  # Use relative path, pointing to current directory
# Or new_root = os.path.abspath(".")  # Use absolute path

def replace_root_in_json(json_file):
    """Replace ROOT_PATH in JSON files"""
    with open(json_file, 'r', encoding='utf-8') as f:
        data = json.load(f)
    
    # Recursively replace ROOT_PATH in strings
    def replace_in_obj(obj):
        if isinstance(obj, dict):
            return {k: replace_in_obj(v) for k, v in obj.items()}
        elif isinstance(obj, list):
            return [replace_in_obj(item) for item in obj]
        elif isinstance(obj, str):
            return obj.replace(old_root, new_root)
        return obj
    
    data = replace_in_obj(data)
    
    # Save modified file (optional: backup original file)
    backup_file = json_file + ".backup"
    if not os.path.exists(backup_file):
        os.rename(json_file, backup_file)
    
    with open(json_file, 'w', encoding='utf-8') as f:
        json.dump(data, f, indent=2, ensure_ascii=False)
    
    print(f"Processed: {json_file}")

# Process all JSON files
for json_file in glob.glob(os.path.join(json_dir, "*.json")):
    replace_root_in_json(json_file)

print("Processing completed!")
```

#### Method 2: Using sed Command (Linux/Mac)

```bash
# Replace [ROOT_PATH] with current directory's absolute path in all JSON files
CURRENT_DIR=$(pwd)
for file in json_file/*.json; do
    sed -i "s|\[ROOT_PATH\]|$CURRENT_DIR|g" "$file"
done
```

#### Method 3: Runtime Dynamic Replacement (Recommended)

If you prefer not to modify the original JSON files, you can replace dynamically in your code:

```python
import json

class CabinSIDataset:
    def __init__(self, json_file, root_path):
        self.root_path = root_path
        with open(json_file, 'r') as f:
            self.data = json.load(f)
    
    def get_item(self, idx):
        item = self.data[idx]
        # Dynamically replace image paths
        images = [p.replace("[ROOT_PATH]", self.root_path) for p in item['images']]
        return {
            'messages': item['messages'],
            'images': images
        }

# Usage example
dataset = CabinSIDataset(
    json_file="json_file/CabinSI_RelCabin_incabin_multiview.json",
    root_path="/path/to/CabinSI/images"
)
```

## Dataset Description

### Task Types

This dataset includes the following types of spatial reasoning tasks:

| Filename | Task Type | Description |
|----------|-----------|-------------|
| `CabinSI_RelCabin_incabin_multiview.json` | In-cabin relative position judgment | Determine the direction of in-cabin objects relative to passengers |
| `CabinSI_RelCabin_outcabin_singleview__Ego3D.json` | Out-cabin scene understanding | Single-view out-cabin spatial reasoning |
| `CabinSI_RefCabin_incabin_multiview.json` | In-cabin target localization | Multi-view in-cabin object detection and localization |
| `CabinSI_RefCabin_incabin_multiview_all_wo_member_box.json` | In-cabin target localization (without member boxes) | Version without member bounding boxes |
| `CabinSI_RefCabin_crosscabin_multiview_w_member_id.json` | Cross-cabin target localization | Target localization in multi-cabin scenarios |
| `CabinSI_RefCabin_crosscabin_multiview_vague_modified_w_member_id.json` | Vague description target localization | Target localization using vague descriptions | -->

### Data Format

Each JSON file contains a list, where each element includes:

```json
{
  "messages": [
    {
      "role": "user",
      "content": "<image><image><image>from my perspective, which direction is the apple? Answer in one or two words."
    },
    {
      "role": "assistant",
      "content": "behind-left"
    }
  ],
  "images": [
    "[ROOT_PATH]/CabinSI/CabinSI_final_version/.../inside_1.jpg",
    "[ROOT_PATH]/CabinSI/CabinSI_final_version/.../inside_2.jpg",
    "[ROOT_PATH]/CabinSI/CabinSI_final_version/.../inside_3.jpg"
  ],
  "member": 1  // Some files contain member ID
}
```

### Direction Labels

Relative position judgment tasks include the following direction labels:
- `left` / `right` - Left / Right
- `front` / `behind` - Front / Behind
- `front-left` / `front-right` - Front-left / Front-right
- `behind-left` / `behind-right` - Behind-left / Behind-right
- `closest` / `farthest` - Closest / Farthest
- `above` / `below` - Above / Below

## Citation

If you use this dataset, please cite:

```bibtex
@dataset{CabinSI,
  title={CabinSI: Omni-Cabin Spatial Reasoning through Explicit Visual Cognitive Maps.},
  author={Qu,Mengxue and Hu,Hengrui and Ma,Mingming and Lei,Ming and Gao,Jie and Ding,Henghui and Zhao,Yao and Wu,Kenn and Wei,Yunchao},
  year={2026},
  booktitle={ECCV},
}
```

## License

This dataset is for academic research use only.

## Contact

For questions, please contact us through the ModelScope page or GitHub Issues.
