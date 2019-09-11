**Web Categorization**

Get the latest Common Crawl [WET files](https://commoncrawl.s3.amazonaws.com/crawl-data/CC-MAIN-2019-35/wet.paths.gz).

Randomly sample X number of files:  
```shuf -n <number_of_files> <wet_file_list> > <wet_file_sampled_list>```

Add 's3://commoncrawl/' prefix to all file links.

Execute:  
```./aws-submit <wet_file_sampled_list> <s3_bucket_name> <subnet_id> <num_input_partitions> <num_output_partitions> <num_ec2_instances>```

Note: you may need to request limit increase to use large number of ec2 instances

Copy the results from s3:  
```aws s3 cp s3://<s3_bucket_name>/output/web_categories spark-warehouse/ --recursive```

Execute:
```
pyspark  
>>>> df = sqlContext.read.parquet("spark-warehouse/web_categories")  
>>>> df.show(50, False)  
>>>> df.groupBy().sum().show() # Total number of pages processed  
```

**Run locally**

$SPARK_HOME/bin/spark-submit ./web-classifier.py --num_output_partitions 1 --log_level WARN <wet_file_sampled_list> web_categories
