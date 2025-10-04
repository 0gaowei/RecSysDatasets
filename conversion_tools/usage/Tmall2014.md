# Tmall2014

## Dataset Information

**For detailed dataset information, please visit:** [Tianchi Tmall Recommendation Dataset](https://tianchi.aliyun.com/dataset/140281)

## Prerequisites

```bash
git clone https://github.com/RUCAIBox/RecDatasets
cd RecDatasets/conversion_tools
pip install -r requirements.txt
```

## Data Conversion

### Basic Usage

```bash
python run.py --dataset tmall_2014 \
  --input_path /path/to/tianchi_2014002_rec_tmall_log_partc.txt \
  --output_path output_data/tmall2014 \
  --interaction_type click \
  --convert_inter
```

### Parameters

- `--dataset`: `tmall_2014` (required)
- `--input_path`: Path to the input data file (required)
- `--output_path`: Directory to store converted files (required)
- `--interaction_type`: `click`, `cart`, `collect`, or `alipay` (required)
- `--convert_inter`: Enable conversion (required)
- `--duplicate_removal`: Enable deduplication (optional)

### Convert All Interaction Types

```bash
for type in click cart collect alipay; do
  python run.py --dataset tmall_2014 \
    --input_path /path/to/data.txt \
    --output_path output_data/tmall2014 \
    --interaction_type $type \
    --convert_inter
done
```

## Output Format

Output file: `output_data/tmall2014/tmall2014-{interaction_type}/tmall2014-{interaction_type}.inter`

```
user_id:token	item_id:token	timestamp:float
u6276408	3903192	1377496871
```

With `--duplicate_removal`:

```
user_id:token	item_id:token	timestamp:float	interactions:float
u6276408	3903192	1377496871	3
```

