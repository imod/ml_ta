# Cluster database tables

This notebook (`db_clustering.ipynb`) tries to cluster database tables by using its column names. 
All columnnames of a table are used to build a document.
This document is build by cleaning (split, lemmatizing, filtering, ...) and concatenating all column names and store the result in the dataframe as `DOCUMENT_CONTENT`.
With multiple models we then try to allocate clusters:

- Latent Dirichlet Allocation (LDA), with a Bag of Words (BoW) Vector representation
- K-Means, with TF-IDF Vectors

There is data for three types of databases (MSSQL, Oracel, MySQL), with a total of more then 17'000'000 records, where each record contains the Metadata of one column:

- Oracle (oracle_metadata.csv)
- MySQL (mysql_metadata.csv)
- MSSQL (mssql_allprod_collumns.csv)

__The input files contain the following fields:__

__DB Metadata__
| Name    | Description |
| -------- | ------- |
| UID  | Unique ID of the owning application    |
| DBType | Type of the Databse (MSSQL, MySQL, Oracle, DB2)     |
| Instance    | (ony MSSQL) The instance of the Database |
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

### Language

To simplify spliting, lemmatization and vectorizing of the columnnames, the data is annotated with the language. 
This annotation is done in a separate notebook: `detect_language.ipynb`.
The resulting data is persisted as a separate ZIP (`db_metadata.zip`) and is used as the input for further processing.


## Procedure
1. load the data
1. treat each table as like a single document with column names as words 
    combine all column-name into a new colum ('text')
1. calculate TF-IDF

   - only include terms with N occurences in all documents (`min_df`)
   - only include terms witch are part of at max 75% of all documents (`max_df`)
   - remove `stop_words` (`english`)
1. use LDA (LatentDirichletAllocation) to build the clusters, this is similar to K-Means
