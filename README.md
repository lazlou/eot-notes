# EOT Web Archive - Parquet Access Notes
Some notes on how to access and query the Parquet data for the End of Term (EOT) Web Archive hosted on AWS S3. Feedback welcome!

**Documentation:** [EOT Archive Notes](https://lazlou.github.io/eot-notes/intro.html)

## Jupyter Notebooks:
- [eot_parquet_access.ipynb](notebooks/eot_parquet_access.ipynb): Notebook showing how to access the Parquet data for the End of Term (EOT) Web Archive hosted on AWS S3. A more detailed description of these steps and overview of the dataset is available here: [EOT Web Archive - Parquet Access Notes](eot_parquet_access.md).
- [how_to_query_eot_data.ipynb](notebooks/how_to_query_eot_data.ipynb): Notebook providing an initial overview of how to view and query the Parquet data with DuckDB.

## Additional Resources:
### EOT Dataset Access
- [End of Term Datasets](https://eotarchive.org/data/): Primary resource with EOT datasets and documentation.
- [EOT S3 Bucket Registry Entry](https://registry.opendata.aws/eot-web-archive/): Main S3 bucket with associated open data registry entry.

### Tutorials
- *[End of Term Parquet Workshop](https://github.com/end-of-term/eot-parquet-workshop)*  by Mark E. Phillips and Sawood Alam (2024): Step-by-step guide on analyzing the EOT WARC data in Parquet format.

### Background Articles
- *[End of Term Web Archive Dataset: Longitudinal Web Archive of .GOV and .MIL Domains](https://digital.library.unt.edu/ark:/67531/metadc2201613/)* by Mark E. Phillips, Kristy Phillips, and Sawood Alam (2024): Overview of dataset characteristics, file formats, intended uses, and methodology.
- *[Content-Based Characterization of the End of Term Web Archive](https://digital.library.unt.edu/ark:/67531/metadc2201623/)* by Mark E. Phillips, Kristy Phillips, and Sawood Alam (2023): Covers derivative creation and metadata management, including the technologies used and introduction of the WARC Metadata Sidecar for storing auxiliary metadata.