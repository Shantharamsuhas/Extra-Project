# Fusion_base

Task
Description:

GDELT is a large public dataset provided by Google.

It basically consists of three parts: Events, Mentions and the Global Knowledge Graph (GKG). The full dataset is in the terabytes and updates every 15 minutes.

For now, we just care about the Events dataset.

Google releases the data every 15 minutes as zipped CSV files which can be found here:

http://data.gdeltproject.org/gdeltv2/lastupdate.txt

Task:

Write a Python script that checks if there is a new release of the Events dataset (the first link in the list). If there is a new dataset available, download the CSV file and save it as Parquet file. The resulting Parquet file should grow incrementally, i.e., it should be updated whenever there is a new release – which is usually every 15 minutes. One important requirement is, that the resulting Parquet file does not contain duplicates.


Proposed Solution:

Hit the URL and check and download the new release.

If its a newer release then dowload it into pandas dataframe. We enforce column names and datatypes as mentioned in official schema document mentioned in the source.

As per link mentioned in the sources, Same news can be reshared multiple times which ends up wt lot of duplicates

We refer to column "SOURCEURL" and use it to drop the duplicates within the current events release

Current events release might also have some duplicates with previous releases. To resolve this we need to check duplicates against entire parquet table, which could become troublesome if table size is huge.

Hence we always check for previous partitions data and compare it with current events data to remove duplicates. Hence for next release the previous partition with have no duplicates and so on. This helps to not scan entire table

After cleaning we make use of dask and pyarrow libraries to write it into parquet file. If parquet file is not present for first time it would be created else we append to the same file.
