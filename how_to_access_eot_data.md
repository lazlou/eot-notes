# Accessing the EOT Archive Data (Parquet Files)
Basic instructions on how to access the Parquet data for the End of Term (EOT) Archive hosted on AWS S3. These notes + approach are a work-in-progress. Open to suggestions for improvement.

**Note:** Currently written for macOS (or Linux/Unix-like environments). I hope to update these later so the commands are easier to test across operating systems.

## Notebooks available:
- [eot_parquet_access.ipynb](notebooks/eot_parquet_access.ipynb): You can download this Jupyter Notebook and run all the example commands locally without needing to copy-paste from here.
- [eot_parquet_access.ipynb](notebooks/how_to_query_eot_data.ipynb): Here's an additional notebook that gives a basic overview showing how to view and query the Parquet data with DuckDB.

## S3 Bucket Overview
The EOT Archive S3 bucket is publicly accessible. You don't need AWS credentials, but you must use the `--no-sign-request` flag with AWS CLI commands.  

### General Structure
- Bucket Name: `eotarchive`
- Path to Parquet files: `eot-index/table/eot-main/crawl=EOT-2020/` *(change the year as needed)*
```
eotarchive/
├── crawl-data/        # Raw WARC/WAT/WET files
└── eot-index/
    ├── collections/
    │   ├── EOT-2004/   # Compressed CDXJ files (.gz)
    │   ├── EOT-2008/
    │   ├── EOT-2012/
    │   ├── EOT-2016/
    │   └── EOT-2020/
    └── table/
        └── eot-main/
            ├── crawl=EOT-2004/  # Parquet part files (.gz.parquet)
            ├── crawl=EOT-2008/
            ├── crawl=EOT-2012/
            ├── crawl=EOT-2016/
            └── crawl=EOT-2020/  
```

## EOT-2020 Parquet Files 
The Parquet files are the converted and compressed versions of the original CDXJ index data. Each `part-XXXXX` file is a chunk (part) of the full EOT-2020 index dataset. These are already ready to be loaded into tools like Pandas, PyArrow, Spark, etc.

### Details
- Number of files: 48 (`part-00000` to `part-00047`)
- Compressed size (total): ~73,316 MB (~71.6 GiB)
- File size range: ~725 MB to ~3.2 GB
- Average size: ~1.5–2.5 GB per file

### Download Speed Estimates*
  - Small file (~725 MB): ~2–3 minutes
  - Large file (~3 GB): ~10–15 minutes 
  - Full dataset (~72 GB): ~3.5–5+ hours

\*Time estimates based on ~4-6 MB/s download speeds.

## Requirements
### 1. Install Required Tools
You'll need `awscli` installed:
```bash
brew install awscli
```

## Explore and Access the EOT Data
### 2. List Bucket Contents (Test Connection)

```bash
aws s3 ls --no-sign-request s3://eotarchive/
```

#### Output:
```
PRE crawl-data/
PRE eot-index/
```

### 3. List Available Parquet Files for a Specific Crawl
This will show you all 48 Parquet part files (`part-00000-\*.gz.parquet`) in the EOT-2020 collection. 
```bash
aws s3 ls --no-sign-request s3://eotarchive/eot-index/table/eot-main/crawl=EOT-2020/
```

Each line looks like:
```
<timestamp> <size> <filename>
```

Example:
```
2023-11-08 09:07:19  725934696 part-00046-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
```

| Field                      | Meaning                                                      |
| -------------------------- | ------------------------------------------------------------ |
| `2023-11-08 09:07:19`      | The last modified date and time of the file in S3 (UTC). |
| `725934696`                | File size in bytes. This one is ~726 MB compressed.    |
| `part-00046-...gz.parquet` | File name: a compressed Parquet part file.               |

#### EOT-2020 Output (Full list of EOT-2020 Parquet files)
```
2023-11-08 09:01:16 3448135711 part-00000-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:01:18 2968471460 part-00001-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:01:22 2416854708 part-00002-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:01:23 2568330760 part-00003-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:01:24 2019064040 part-00004-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:02:10 2049308445 part-00005-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:02:20 2026764211 part-00006-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:02:25 1945399315 part-00007-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:02:27 1940509056 part-00008-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:02:37 1928684657 part-00009-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:01 1993549968 part-00010-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:10 2722040758 part-00011-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:14 1926880962 part-00012-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:16 1853413426 part-00013-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:25 1798117192 part-00014-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:03:51 1767759290 part-00015-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:02 1763567305 part-00016-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:02 1714640093 part-00017-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:09 1657250685 part-00018-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:16 1601393518 part-00019-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:33 1646659046 part-00020-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:42 1596816861 part-00021-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:42 1590841948 part-00022-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:47 1522885885 part-00023-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:04:52 1564288165 part-00024-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:11 1819595077 part-00025-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:18 1497803634 part-00026-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:19 1490407961 part-00027-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:22 1493182381 part-00028-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:30 1398586176 part-00029-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:53 1354908761 part-00030-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:55 1354073376 part-00031-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:55 1370434591 part-00032-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:05:58 1379782218 part-00033-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:02 1282713381 part-00034-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:26 1232302068 part-00035-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:27 1283149573 part-00036-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:28 1047651424 part-00037-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:32 1060767172 part-00038-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:34  993121623 part-00039-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:55  926045582 part-00040-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:57  965251436 part-00041-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:58  923344888 part-00042-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:58  930140940 part-00043-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:06:59  815602721 part-00044-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:07:18  753819149 part-00045-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:07:19  725934696 part-00046-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
2023-11-08 09:07:21  747395388 part-00047-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet
```
### 4. Create Local Folder for Subset Downloads
First, create a folder to organize your downloaded subsets: *(replace with your output directory path)*
```bash
mkdir -p EOT-2020/parquet/subset
```

### 5. Download a Specific Parquet File (Single Test)
This downloads the file to your local machine and saves inside `EOT-2020/parquet/subset/` (unless specified otherwise).

Then download:
```bash
aws s3 cp --no-sign-request \
  s3://eotarchive/eot-index/table/eot-main/crawl=EOT-2020/part-00046-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet \
  EOT-2020/parquet/subset/
```

### 6. Load and Inspect the Parquet File
**Important:** The EOT Parquet files may use deprecated INT96 timestamp formats. To avoid errors, Python (`pandas.read_parquet`) is recommended over `parquet-cli` for inspection.

Example script (`preview_parquet.py`):
```python
import pandas as pd

# Load the Parquet file
df = pd.read_parquet("EOT-2020/parquet/subset/part-00046-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet")

# Show the schema (columns and dtypes)
print(df.dtypes)

# Preview first few rows
print(df.head())
```

#### Output for `part-00046` file:
```
url_surtkey                           object
url                                   object
url_host_name                         object
url_host_tld                          object
url_host_2nd_last_part                object
url_host_3rd_last_part                object
url_host_4th_last_part                object
url_host_5th_last_part                object
url_host_registry_suffix              object
url_host_registered_domain            object
url_host_private_suffix               object
url_host_private_domain               object
url_host_name_reversed                object
url_protocol                          object
url_port                             float64
url_path                              object
url_query                             object
fetch_time                    datetime64[ns]
fetch_status                           int16
content_digest                        object
content_mime_type                     object
content_mime_detected                 object
content_charset                       object
content_languages                     object
content_puid                          object
warc_filename                         object
warc_record_offset                     int64
warc_record_length                     int64
warc_segment                          object
crawl                                 object
subset                                object
dtype: object
                                         url_surtkey  ... subset
0  com,usarmyjrotc)/news/21/03/scripts/legacy/scr...  ...   warc
1  com,usarmyjrotc)/news/21/03/scripts/legacy/scr...  ...   warc
2  com,usarmyjrotc)/news/21/03/scripts/legacy/scr...  ...   warc
3  com,usarmyjrotc)/news/21/03/scripts/legacy/scr...  ...   warc
4  com,usarmyjrotc)/news/21/03/scripts/legacy/scr...  ...   warc

[5 rows x 31 columns]
```

### 7. Optional: Download the Full Dataset
**Warning:** This will download all 48 Parquet files for EOT-2020 (~72 GB compressed). Test on smaller subsets first!

Create a folder to save the output files:
```bash
mkdir -p EOT-2020/parquet/full
```

Then download:
```bash 
aws s3 sync --no-sign-request \
  s3://eotarchive/eot-index/table/eot-main/crawl=EOT-2020/ \
  EOT-2020/parquet/full/
```

### 8. Optional: Download Only a Subset (e.g., first 5 files)
Create a folder to save the output files:
```bash
mkdir -p EOT-2020/parquet/subset/
```

Then download: *(repeat for part-00001, part-00002, etc.)*
```bash
aws s3 cp --no-sign-request \
  s3://eotarchive/eot-index/table/eot-main/crawl=EOT-2020/part-00047-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet \
  EOT-2020/parquet/subset/

aws s3 cp --no-sign-request \
  s3://eotarchive/eot-index/table/eot-main/crawl=EOT-2020/part-00045-dda73194-fd75-4dcb-b361-4d099d882262-c000.gz.parquet \
  EOT-2020/parquet/subset/
```

## Additional info
- [EOT Datasets Website](https://eotarchive.org/data/)
- [End of Term Web Archive Dataset: Longitudinal Web Archive of .GOV and .MIL Domains](https://doi.org/10.1109/JCDL57899.2023.00024)
- [End of Term Parquet Workshop](https://github.com/end-of-term/eot-parquet-workshop)
