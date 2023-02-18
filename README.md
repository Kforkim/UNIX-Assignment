# UNIX Assignment

## Data Inspection

### Attributes of `fang_et_al_genotypes`

```
$ wc fang_et_al_genotypes.txt
$ du -h fang_et_al_genotypes.txt
$ awk -F "\t" '{print NF;exit}' fang_et_al_genotypes.txt
```

By inspecting this file I learned that:

1. There are 2783 lines, 2744038 words, and 11051939 characters.
2. File size is 6.1M.
3. There are 986 columns in this file.
   1. Column 1 is sample IDs.
   2. Column 3 is sample's group.
   3. First line from column 4 is SNP IDs.

### Attributes of `snp_position.txt`

```
$ wc snp_position.txt
$ du -h snp_position.txt
$ awk -F "\t" '{print NF;exit}' snp_position.txt
```

By inspecting this file I learned that:

1. There are 984 lines, 13198 words, and 82763 characters.
2. File size is 38k
3. There are 15 columns in this file.
4. The first line is the header.

## Data Processing

### Maize Data

```
$ awk 'NR==1 || /ZMM/' fang_et_al_genotypes.txt > maize_genotypes.txt
$ cut -f 4- maize_genotypes.txt > maize_cut.txt
$ awk -f transpose.awk maize_cut.txt | sort -k1,1 > maize_sort.txt
$ cut -f 1,3,4 snp_position.txt | tail -n +2 |sort -k1,1 > snp_sort.txt
$ join -t $'\t' -1 1 -2 1 snp_sort.txt maize_sort.txt > maize_snp.txt
$ grep unknown maize_snp.txt | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > Maize_Unknown.txt
$ grep "multiple" maize_snp.txt | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}'> Maize_Multiple.txt
$ for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' maize_snp.txt | sort -k3,3n | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > mchr"$i"_inc.txt; done
$ for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' maize_snp.txt | sort -k3,3nr | sed 's/?/-/g' | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > mchr"$i"_dec.txt; done

```

1. Line one keeps row 1 and looks for ZMM in the `fang_et_al_genotypes.txt` (representing maize) and saves it in a new file `maize_genotypes.txt`

2. Line 2 deletes the first 3 columns in the `maize_genotypes.txt` and unnecessary information such as Sample ID and group will no longer be a part of the file. 
3. Line 3 transposes the new `maize_cut` file and sorts it based on the SNP_ID column.
4. Line 4 deletes all columns except columns 1, 3 and 4 in the `snp_position.txt` and then removes the header using `tail` command, and then sorts it based on the SNP_ID column.
5. Lines 5 joins the two `snp_sort.txt` and `maize_sort.txt` , merging the first column of each file.
6. Line 6 and 7 looks for the words `unknown` and `multiple` , adds headers to the files and saves it in `Maize_Unknown.txt` and `Maize_Multiple.txt`
7. Line 8 and 9 looks for numbers from 1-10 in the chromosome column in the `maize_snp.txt`, then for each set of data with the same chromosome number, it sorts based on position column in increasing or decreasing order, changes `?` to `-` (in line 9), adds the headers and then saves them in a file with their corresponsing chromosome number in the name `mchr[i]`. 

### Teosinte Data

```
$ awk 'NR==1 || /ZMP/' fang_et_al_genotypes.txt > teosinte_genotypes.txt
$ cut -f 4- teosinte_genotypes.txt > teosinte_cut.txt
$ awk -f transpose.awk teosinte_cut.txt | sort -k1,1 > teosinte_sort.txt
$ join -t $'\t' -1 1 -2 1 snp_sort.txt teosinte_sort.txt > teosinte_snp.txt
$ grep unknown teosinte_snp.txt | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > Teosinte_Unknown.txt
$ grep "multiple" teosinte_snp.txt | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}'> Teosinte_Multiple.txt
$ for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' teosinte_snp.txt | sort -k3,3n | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > tchr"$i"_inc.txt; done
$ for ((i=1; i<=10; i++)); do awk -v i="$i" '{if ($2==i) print $0}' teosinte_snp.txt | sort -k3,3nr | sed 's/?/-/g' | awk -v OFS='\t' 'BEGIN{print "SNP_ID", "Chromosome", "Position", "Genotype_data"}{print}' > tchr"$i"_dec.txt; done
```

Code works the same as described in the previous section.




