#UNIX Assignment

##Data Inspection

###Attributes of fang_et_al_genotypes

```sh
wc fang_et_al_genotypes.txt
 
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt

less fang_et_al_genotypes.txt

du -h fang_et_al_genotypes.txt

```

By inspecting this file I learned that:

1. 2783 Newline, 2744038 words, and 11051939 bytes counted in fang_et_al_genotypes.txt
2. There are 986 columns in fang_et_al_genotypes.txt and it appears to be tab-delimited 
3. The file size is 6.7M for fang_et_al_genotypes.txt


###Attributes of snp_position.txt

```sh
wc snp_position.txt

awk -F "\t" '{print NF; exit}' snp_position.txt

du -h snp_position.txt

```

By inspecting this file I learned that:

1.   984 Newline, 13198 words, and 82763  bytes counted in snp_position.txt
2. There are 15 columns in snp_position.txt
3. The file size is 49K for snp_position.txt


##Data Processing

###Maize Data

```sh

cut -f1,3,4 snp_position.txt | sed '1d' | sort -k1,1 > snp_position_sort.txt

awk 'NR==1 || /ZMM/' fang_et_al_genotypes.txt | cut -f 4-986 > maize_geno_sort.txt

awk -f transpose.awk maize_geno_sort.txt  > transposed_maize_ZMM.txt

sort -k1,1 transposed_maize_ZMM.txt > maize_sort.txt

join -t $'\t' -1 1 -2 1 snp_position_sort.txt maize_sort.txt > snp_maize_sort.txt

awk -v OFS='\t' 'BEGIN {print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' snp_maize_sort.txt | awk 'NR==1 || /unknown/' > unknown_maize_snp.txt

awk -v OFS='\t' 'BEGIN {print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' snp_maize_sort.txt | awk 'NR==1 || /multiple/' > multiple_maize_snp.txt

for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' snp_maize_sort.txt | sort -k3,3n | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > chr"$i"_maize_increasing.txt; done

for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' snp_maize_sort.txt | sort -k3,3nr | sed 's/?/-/g' | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > chr"$i"_maize_decreasing.txt; done

```

* First we cut out the required columns from the snp_position.txt file piped to chop off the headers, and piped to sort by SNP_ID.
* The code then searches the feng_et_al_genotypes.txt for matches of "ZMM", the maize group ZMMIL/ZMMLR/ZMMMR, and then cuts out the headers. Then we transpose and sort the file by SNP_ID. 
* The two files created are then joined together.
* Headers for SNP_ID, Chromosome, Position, and Genotype_data are added back in for labeling and then checked for missing or if found in multiple positions.
* Lastly, a for loop is used to go through multiple files iteratively based on chromosome. The files have headers for SNP_ID, Chromosome, Position, and Genotype_data added back in and the files are sorted by increasing/decreasing position. Files with decreasing position have ? replaced by - for missing data.



###Teosinte Data

```sh
cut -f1,3,4 snp_position.txt | sed '1d' | sort -k1,1 > snp_position_sort.txt

awk 'NR==1 || /ZMP/' fang_et_al_genotypes.txt | cut -f 4-986 > tes_geno_sort.txt

awk -f transpose.awk tes_geno_sort.txt  > transposed_tes_ZMP.txt

sort -k1,1 transposed_tes_ZMP.txt > tes_sort.txt

join -t $'\t' -1 1 -2 1 snp_position_sort.txt tes_sort.txt > snp_tes_sort.txt

awk -v OFS='\t' 'BEGIN {print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' snp_tes_sort.txt | awk 'NR==1 || /unknown/' > unknown_tes_snp.txt

awk -v OFS='\t' 'BEGIN {print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' snp_tes_sort.txt | awk 'NR==1 || /multiple/' > multiple_tes_snp.txt

for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' snp_tes_sort.txt | sort -k3,3n | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > chr"$i"_tes_increasing.txt; done

for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' snp_tes_sort.txt | sort -k3,3nr | sed 's/?/-/g' | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > chr"$i"_tes_decreasing.txt; done


```

* First we cut out the required columns from the snp_position.txt file piped to chop off the headers, and piped to sort by SNP_ID.
* The code then searches the feng_et_al_genotypes.txt for matches of "ZMP", the teosinte group ZMPBA/ZMPIL/ZMPJA, and then cuts out the headers. Then we transpose and sort the file by SNP_ID. 
* The two files created are then joined together.
* Headers for SNP_ID, Chromosome, Position, and Genotype_data are added back in for labeling and then checked for missing or if found in multiple positions.
* Lastly, a for loop is used to go through multiple files iteratively based on chromosome. The files have headers for SNP_ID, Chromosome, Position, and Genotype_data added back in and the files are sorted by increasing/decreasing position. Files with decreasing position have ? replaced by - for missing data.
