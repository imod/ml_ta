# analyse the data

rai_allprod_collumns.csv
small.csv

UID,DBType,Instance,DBName,Schema,Table,Column,ColumnType

```
# count number of coulums per database
cut -f4 -d, small.csv | sort | uniq -c
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c > stats_coulmns_per_db.txt
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c 
```
```
# count number of tables per database
cut -f6 -d, small.csv | sort | uniq -c
cut -f6 -d, rai_allprod_collumns.csv | sort | uniq -c > stats_coulmns_per_table.txt
cut -f4 -d, rai_allprod_collumns.csv | sort | uniq -c 
```

# Extract the second column into a new file
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

# Prompt

the file rai_allprod_collumns_clean.csv contains the metainformation about all Databases, Tables and Columns structured in a CSV file like this:
UID,DBType,Instance,DBName,Schema,Table,Column,ColumnType

use machine learning to:
- Group similar tables/columns across databases
- Identify columns likely to contain sensitive information based on naming patterns
- Flag potential compliance issues (PII, financial data, etc.)


# Online

- K-Means Clustering Text Documents: Python in Excel Tutorial (Free Files) https://www.youtube.com/watch?v=55IKVSB5AoM
- Topic Modeling Text Documents With LDA: Python in Excel Tutorial (Free Files) https://www.youtube.com/watch?v=b91ohJvEst4
- Split words: Viterbi algorithm, https://en.wikipedia.org/wiki/Viterbi_algorithm `wordninja.split('TOTALTIME')` (default support for english)

# PII data
- https://docs.aws.amazon.com/de_de/sns/latest/dg/sns-message-data-protection-sensitive-data-types-pii.html
- https://learn.microsoft.com/en-us/purview/sit-sensitive-information-type-entity-definitions
  - https://learn.microsoft.com/en-us/purview/sit-defn-switzerland-ssn-ahv-number


# procedure
1. load the data
1. treat each table as like a single document with column names as words 
    combine all column-name into a new colum ('text')
1. calculate TF-IDF

   - only include terms with N occurences in all documents (`min_df`)
   - only include terms witch are part of at max 75% of all documents (`max_df`)
   - remove `stop_words` (`english`)
1. use LDA (LatentDirichletAllocation) to build the clusters, this is similar to K-Means


# Fragen
- was/wo/wie/wann?
  word2vec, kmean, lda, tf-idf?