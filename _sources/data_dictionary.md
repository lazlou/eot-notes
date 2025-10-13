# Data Dictionary

##  EOT-2020 Dataset Overview 
| File             | Total Size Compressed      |
| ---------------- | -------------------------- |
| WARC files       | 266.04 TB (239,811 files)  |
| WAT files        | 9.15 TB                    |
| WET files        | 2.6 TB                     |
| META files       | 712.66 GB                  |
| CDX files        | 83.66 GB                   |
| Parquet files    | 76.88 GB (48 files)        |
| URL Index files  | 74.4 GB (49 files)         |

Source: [End of Term Web Archive 2020](https://eotarchive.org/data/data-2020/) {cite:p}`eot_dataset_2020`


## EOT Parquet Data Dictionary
This data dictionary is based on the [EOT Index Schema](https://github.com/commoncrawl/cc-index-table/blob/main/src/main/resources/schema/eot-index-schema.json), available in JSON format on the Common Crawl Index Table repo {cite:p}`common_crawl_eot_2024`. The WARC Metadata sidecar fields (added by the EOT Archive team in 2023) are currently not included in that schema file and have been added to this data dictionary for reference and schema completeness. They include: `content_mime_detected`, `content_charset`, and `content_languages`, `content_puid`. For more information about the development and use of the WARC Metadata Sidecar fields, see [Phillips, Phillips, and Alam (2023)](https://digital.library.unt.edu/ark:/67531/metadc2201623/) {cite:p}`phillips_content-based_2023`.

In the data dictionary below, the **Schema Data Type** column reflects the data types as defined in the original JSON schema. The **Pandas Data Type** column represents how the data is interpreted and loaded by Pandas after reading the Parquet file using `pandas.read_parquet()`. 

| Column Name | Schema Data Type | Pandas Data Type | Nullable | Description | From CDX | Example |
|-------------|------------------|------------------|----------|-------------|----------|---------|
| url_surtkey | string | object | False | SURT URL key | N/A | com,example)/path/index.html | 
| url | string | object | False | URL string | url | [https://www.example.com/path/index.html](https://www.example.com/path/index.html) | 
| url_host_name | string | object | False | Hostname, including IP addresses | N/A | [www.example.com](http://www.example.com) | 
| url_host_tld | string | object | True | Top-level domain or last part of the hostname | N/A | com for the hostname [www.example.com](http://www.example.com) | 
| url_host_2nd_last_part | string | object | True | Second last part of the hostname | N/A | example for the hostname [www.example.com](http://www.example.com), co for bbc.co.uk | 
| url_host_3rd_last_part | string | object | True | Third last part of the hostname | N/A | www for the hostname [www.example.com](http://www.example.com) | 
| url_host_4th_last_part | string | object | True | 4th last part of the hostname | N/A | host1 for host1.subdomain.example.com | 
| url_host_5th_last_part | string | object | True | 5th last part of the hostname | N/A | host1 for host1.sub2.subdomain.example.com | 
| url_host_registry_suffix | string | object | True | Domain registry suffix | N/A | com, co.uk | 
| url_host_registered_domain | string | object | True | Domain name of the host (one level below the registry suffix) | N/A | example.com, bbc.co.uk | 
| url_host_private_suffix | string | object | True | Suffix of domain registries including private registrars, see [https://publicsuffix.org/](https://publicsuffix.org/) | N/A | com, co.uk, but also s3.amazonaws.com or blogspot.com | 
| url_host_private_domain | string | object | True | Domain name of the host (one level below the private suffix) | N/A | mypublicbucket.s3.amazonaws.com or myblog.blogspot.com | 
| url_host_name_reversed | string | object | True | Hostname, excluding IP addresses, in reverse domain name notation | N/A | com.example.www | 
| url_protocol | string | object | False | Protocol of the URL | N/A | https | 
| url_port | integer | float64 | True | Port of the URL (null if not explicitly specified in the URL) | N/A | 8443 | 
| url_path | string | object | True | File path of the URL | N/A | /path/index.html | 
| url_query | string | object | True | Query part of the URL | N/A | q=abc&lang=en for .../search?q=abc&lang=en | 
| fetch_time | timestamp | datetime64[ns] | False | Fetch time (capture time stamp) | N/A | 2017-10-24T00:14:32Z | 
| fetch_status | short | int16 | False | HTTP response status code (-1 if absent, eg. for revisit records) | status | 200 | 
| content_digest | string | object | True | SHA-1 content digest (WARC-Payload-Digest) | digest | CH7IV3XAD3M7A42JARKRLJ3T5PGGCGXD | 
| content_mime_type | string | object | True | Content-Type sent in HTTP response header | mime | text/html | 
| warc_filename | string | object | False | WARC filename/path below s3://eotarchive/ or [https://eotarchive.s3.amazonaws.com/](https://eotarchive.s3.amazonaws.com/) | filename | crawl-data/EOT-2008/segments/IA-001/warc/DOTGOV-2008-01-20080923002742-04410-crawling14.us.archive.org.arc.gz |
| warc_record_offset | long | int64 | False | Offset of the WARC record | offset | 397346194 | 
| warc_record_length | long | int64 | False | Length of the WARC record | length | 24662 | 
| warc_segment | string | object | False | Segment the WARC file belongs to | N/A | IA-001 | 
| crawl | string | object | False | Crawl the capture/record is part of | N/A | EOT-2008 | 
| subset | string | object | False | Subset of responses (organized as subfolder of segments) | N/A | N/A | 
| content_mime_detected | string | object | True | MIME type detected from content-based analysis using python-magic. Typically more general than the MIME type from HTTP headers or FIDO. | N/A | text/html | 
| content_charset | string | object | True | Detected character encoding of the content using Chardet. Useful when HTTP headers are missing or incorrect. | N/A | UTF-8 | 
| content_languages | string | object | True | List of top detected languages from HTML or text content, using Compact Language Detector 2 (CLD2). | N/A | en, es | 
| content_puid | string | object | True | PRONOM Persistent Unique Identifier for file format, determined using the FIDO format identification tool. | N/A | fmt/19 | 