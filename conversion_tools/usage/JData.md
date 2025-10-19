# JData

## Dataset Information

**Input File**: `/root/autodl-tmp/cjw/MBR-Mamba/dataset/JData/JData_Action_merged.csv`

**Original Format**:
```
user_id,sku_id,time,model_id,type,cate,brand,month
266079.0,138778,2016-01-31 23:59:02,,1,8,403,2016-02
```

**Behavior Types**: 
- `1` → `pv` (浏览商品详情页)
- `2` → `cart` (加入购物车)
- `4` → `buy` (下单)
- `5` → `fav` (关注)
- `6` → `click` (点击)
- 排除 `3` (购物车删除)

**Data Size**: ~50.6M records

## Prerequisites

```bash
git clone https://github.com/0gaowei/RecSysDatasets
cd RecSysDatasets/conversion_tools
pip install -r requirements.txt
```

## Data Conversion

### Basic Usage (Convert All Interaction Types - Merged)

```bash
python run.py --dataset jdata \
  --input_path /path/to/JData_Action_merged.csv \
  --output_path /path/to/output_directory \
  --convert_inter
```

### Convert Single Interaction Type

```bash
python run.py --dataset jdata \
  --input_path /path/to/JData_Action_merged.csv \
  --output_path /path/to/output_directory \
  --interaction_type pv \
  --convert_inter
```

### Parameters

- `--dataset`: `jdata` (required)
- `--input_path`: Path to the input data file (required)
- `--output_path`: Base directory to store converted files (e.g., `/root/autodl-tmp/cjw/MBR-Mamba/dataset`). The tool will create `Rec_JData/processed/jdata-{type}/` under this path. (required)
- `--interaction_type`: `pv`, `cart`, `buy`, `fav`, `click`, or omit for `all` (merged types).
- `--convert_inter`: Enable conversion (required)
- `--duplicate_removal`: Enable deduplication (optional)

**Note**: When `--interaction_type` is omitted, all five interaction types (pv, cart, buy, fav, click) will be merged into a single file with an additional `action_type` column.

## Output Format

### Single Interaction Type (e.g., `jdata-pv`)
Output file: `output_path/Rec_JData/processed/jdata-pv/jdata-pv.inter`

```
user_id:token	item_id:token	timestamp:float
266079	138778	1454255942
```

### All Interaction Types (Merged) (`jdata-merged`)
Output file: `output_path/Rec_JData/processed/jdata-merged/jdata-merged.inter`

```
user_id:token	item_id:token	action_type:token	timestamp:float
266079	138778	pv	1454255942
```

### With `--duplicate_removal`

#### Single Type:
```
user_id:token	item_id:token	timestamp:float	interactions:float
266079	138778	1454255942	3
```

#### Merged Types:
```
user_id:token	item_id:token	action_type:token	timestamp:float	interactions:float
266079	138778	pv	1454255942	1
```

## Data Processing Details

### Time Format Conversion
- **Input**: `2016-01-31 23:59:02` (YYYY-MM-DD HH:MM:SS)
- **Output**: `1454255942` (Unix timestamp in seconds)

### Behavior Type Mapping
- **Type 1** (浏览) → `pv` (page view)
- **Type 2** (加购) → `cart` (add to cart)
- **Type 4** (下单) → `buy` (purchase)
- **Type 5** (关注) → `fav` (favorite)
- **Type 6** (点击) → `click` (click)
- **Type 3** (购物车删除) → **Excluded** (negative behavior)

### Field Selection
Only basic fields are included in the output:
- `user_id`: User identifier
- `item_id`: Item identifier (sku_id)
- `timestamp`: Unix timestamp
- `action_type`: Behavior type (only in merged mode)

Additional fields (cate, brand, model_id) are excluded to maintain consistency with other datasets.

## Example Commands

### Convert all behavior types (merged)
```bash
python run.py --dataset jdata \
  --input_path /root/autodl-tmp/cjw/MBR-Mamba/dataset/JData/JData_Action_merged.csv \
  --output_path /root/autodl-tmp/cjw/MBR-Mamba/dataset \
  --convert_inter
```

### Convert only page views
```bash
python run.py --dataset jdata \
  --input_path /root/autodl-tmp/cjw/MBR-Mamba/dataset/JData/JData_Action_merged.csv \
  --output_path /root/autodl-tmp/cjw/MBR-Mamba/dataset \
  --interaction_type pv \
  --convert_inter
```

### Convert with deduplication
```bash
python run.py --dataset jdata \
  --input_path /root/autodl-tmp/cjw/MBR-Mamba/dataset/JData/JData_Action_merged.csv \
  --output_path /root/autodl-tmp/cjw/MBR-Mamba/dataset \
  --interaction_type buy \
  --duplicate_removal \
  --convert_inter
```

## Output Structure

```
output_path/
└── Rec_JData/
    └── processed/
        ├── jdata-pv/
        │   └── jdata-pv.inter
        ├── jdata-cart/
        │   └── jdata-cart.inter
        ├── jdata-buy/
        │   └── jdata-buy.inter
        ├── jdata-fav/
        │   └── jdata-fav.inter
        ├── jdata-click/
        │   └── jdata-click.inter
        └── jdata-merged/
            └── jdata-merged.inter
```
