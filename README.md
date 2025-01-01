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
# remove first linecat
#sed '1d' rai_allprod_collumns.csv > rai_allprod_collumns_clean.csv
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
- Split words: Viterbi algorithm, https://en.wikipedia.org/wiki/Viterbi_algorithm `wordninja.split('TOTALTIME')`
