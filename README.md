# Cluster database tables

This notebook (`rai_db_clustering.ipynb`) tries to cluster database tables by using its column names. 
All columns (not rows!) of a table will be used to build a document.
This document is build by cleaning (split, lemmatizing, filtering, ...) and concatenating all column names and store the result in the dataframe as `DOCUMENT_CONTENT`.
With multiple models we then try to allocate clusters:

- Latent Dirichlet Allocation (LDA)
- k-Means 

We have data for three types of databases, with a total of more then 17'000'000 records, where each record contains the definition of one column:

- Oracle (oracle_metadata.csv)
- MySQL (mysql_metadata.csv)
- MSSQL (mssql_allprod_collumns.csv)

__The input files contain the following fields:__

__DB Metadata__
| Name    | Description |
| -------- | ------- |
| UID  | Unique ID of the owning application    |
| DBType | Type of the Databse (MSSQL, MySQL, Oracle, DB2)     |
| Instance    | The instance of the Ddtabase |
| DBName | The name of the database |
| Schema | The schema where the table os located in the database |
| Table | The name of the table |
| Column | The name of a single column |
| ColumnType | The datatyp of the column |

Each table (and therefore also each column) is assiciated to an application in the Enterprise Application Model (EAM). This data is stored in a file called `UID_AppName_Mapping.csv`

__Application Metadata__
| Name    | Description |
| -------- | ------- |
|Anwendung|The name of the application|
|UID|Unique ID of the application    |
|Stereotyp RCH| the type application (e.g. Business-Applikation, Infrastructure) |
|Massen-CID|whether the application deals with CID|

### analysing the data

columns (MSSQL):
`UID,DBType,Instance,DBName,Schema,Table,Column,ColumnType`

#### count number of coulums per database
```
cut -f4 -d, small.csv | sort | uniq -c
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c > stats_coulmns_per_db.txt
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c 
```
#### count number of tables per database
```
cut -f6 -d, small.csv | sort | uniq -c
cut -f6 -d, rai_allprod_collumns.csv | sort | uniq -c > stats_coulmns_per_table.txt
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c 
```

#### Extract the second column into a new file
cut -d, -f2 rai_allprod_collumns_clean.csv > second_column.csv

cleanup columntype
```
# ColumnType: replace every ',' enclosed in brackets '()' with (;)
sed -E 's/\(([^)]*),([^)]*)\)/(\1;\2)/g' rai_allprod_collumns.csv > rai_allprod_collumns_clean.csv.1
mv rai_allprod_collumns_clean.csv.1 rai_allprod_collumns_clean.csv
```

```
input_file="rai_allprod_collumns_clean.csv"
output_file="rai_allprod_collumns_with_dbname_schema.csv"

awk -F, 'BEGIN {OFS=","} {if (NR==1) {print $0, "DBName_Schema"} else {print $0, $4"_"$5}}' $input_file > $output_file
```

## Online research

- K-Means Clustering Text Documents: Python in Excel Tutorial (Free Files) https://www.youtube.com/watch?v=55IKVSB5AoM
- Topic Modeling Text Documents With LDA: Python in Excel Tutorial (Free Files) https://www.youtube.com/watch?v=b91ohJvEst4
- Split words: Viterbi algorithm, https://en.wikipedia.org/wiki/Viterbi_algorithm `wordninja.split('TOTALTIME')` (default support for english)

## CID/PII data
- https://docs.aws.amazon.com/de_de/sns/latest/dg/sns-message-data-protection-sensitive-data-types-pii.html
- https://learn.microsoft.com/en-us/purview/sit-sensitive-information-type-entity-definitions
  - https://learn.microsoft.com/en-us/purview/sit-defn-switzerland-ssn-ahv-number


## Procedure
1. load the data
1. treat each table as like a single document with column names as words 
    combine all column-name into a new colum ('text')
1. calculate TF-IDF

   - only include terms with N occurences in all documents (`min_df`)
   - only include terms witch are part of at max 75% of all documents (`max_df`)
   - remove `stop_words` (`english`)
1. use LDA (LatentDirichletAllocation) to build the clusters, this is similar to K-Means


# Questions
- when to use: word2vec, kmean, lda, tf-idf?