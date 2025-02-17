## 1. Do you think that in a phylogenetic tree the parasites that use similar hosts will group together?

I think it is a reasonable hypothesis that parasites with similar hosts would be closer together in a phylogenetic tree. Parasites use the the cell machinery of the hosts and the machinery of these cells will be more similar with more similar hosts. 

## 2. Why?
Theoretically bird scaffolds should have higher GC content, but here small ones will remain because there is a higher chance for random GC content if you sequence is smaller, the longer it is the harder it would be.

## 3. Insert the missing data in the table below. Use bash, not internet!


|  Species | Host | Genome size | Genes | Genomic GC |
| ------------- | ------------- | ------------- | ------------- | ------------- | 
|1 Plasmodium berghei 	|	rodents	|	20493455 |	4050	| .28541442281221172004 |
|2 Plasmodium cynomolgi 	|	macaques	|17954629	| 7282	| .25074910760397063387 |
|3 Plasmodium falciparum | 	humans	|	26181343 |	5787	| .42160775686953995001 |
|4 Plasmodium knowlesi |		lemures	|	23270305 |	5207	| .23901099876307077281 |
|5 Plasmodium vivax 	|	humans	|	23462346 |	4953	| .40271397184768355577 |
|6 Plasmodium yoelii |		rodents		|27007701 |	5682	| .46163055324301189190 |
|7 Haemoproteus tartakovskyi |	birds	|	22222369 |	(edited) 3910	| .23770659760824166960 |
|8 Toxoplasma gondii 	|	humans	|	128105889	| 15892	| .58519328143783770325 |

```bash=
#for loop through each file, genome size
for file in *.genome; do name=$(echo ${file%.genome});count=$(cat $file | grep -v \>| tr -d '\n'  | wc -m); echo $name $count; done
Haemoproteus_tartakovskyi 20493455
Plasmodium_berghei 17954629
Plasmodium_cynomolgi 26181343
Plasmodium_faciparum 23270305
Plasmodium_knowlesi 23462346
Plasmodium_vivax 27007701
Plasmodium_yoelii 22222369
Toxoplasma_gondii 128105889

#for loop through each file, gene
for file in *.fna; do name=$(echo ${file%.fna});count=$(cat $file | grep \>| wc -l); echo $name $count; done
Ht 4050
Pb 7282
Pc 5787
Pf 5207
Pk 4953
Pv 5682
Py 4919
Tg 15892

#for loop through gc content
for file in *.fna; do name=$(echo ${file%.fna});count=$( echo $(cat $file | grep -v \> |tr -cd  "CGcg" |wc -m)/$(cat $file | grep -v \> |tr -d '\n' |  wc -m) |bc -l); echo $name $count; done
Ht .28541442281221172004
Pb .25074910760397063387
Pc .42160775686953995001
Pf .23901099876307077281
Pk .40271397184768355577
Pv .46163055324301189190
Py .23770659760824166960
Tg .58519328143783770325

```


## 4. Compare the genome sizes with other eukaryotes and bacteria. Discuss with your partner (that is student partner) the reason for the observed genome sizes.
For bacteria the average genome size is 5Mb [reference](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4361730/). 10Mb to >100 000Mb is average genome size for eukaryotes [reference](https://www.sciencedirect.com/topics/biochemistry-genetics-and-molecular-biology/genome-size).
Our average genome size is ~20Mb so small for a eukaryote. One possible explanation for the smal genome is that plasmodium are parasites, which can use host cell proteins/machinery for its own purposes.

## 5. What may cause the biased GC-contents in some of the species?

One reason that there is biased GC-contents is that perhaps there was horizontal gene transfer.
Perhaps there is bias gene expression in the organism.

## 6. What does the curly braces notation stand for?
In bash it creates a list.

## 7. Compare how many BUSCOs (orthologues proteins) that arefound in each proteome. Do the investigated parasites have close to complete numbers of BUSCOs?
(CHECK)
```bash=
#for loop for each species
for file in *.faa; do count=$(cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv | grep -v '#'| grep -v "Missing"| grep -v "Fragmented" | cut -f 1 | sort -u | wc -l); echo ${file%.faa} $count ;done
# Ht 326
# Pb 372
# Pc 429
# Pf 436
# Pk 323
# Pv 437
# Py 434
# Tg 384
```
We have the fewest BUSCO genes in Pk(323) and the most in Pv(437). The complete number of BUSCO genes is 446. I do not know the threshold for whether something is close to complete but 437/446 is pretty good and is 326/446 is not bad.
```
#now to see what percent are complete/ duplicate
for file in *.faa; do count=$(echo $(cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv |grep -v "#" | grep -v "Missing"| grep -v "Fragmented" | cut -f 1 | sort -u | wc -l)/ $(cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv | grep -v '#' |  cut -f 1 | sort -u | wc -l)|bc -l); echo ${file%.faa} $count ;done
# Ht .73094170403587443946
# Pb .83408071748878923766
# Pc .96188340807174887892
# Pf .97757847533632286995
# Pk .72421524663677130044
# Pv .97982062780269058295
# Py .97309417040358744394
# Tg .86098654708520179372
```
Here we see that percentage wise, the fewest BUSCO genes were found in Pk(0.72) and the most Pv(0.98). Again, unsure of threshhold, but does not seem bad.


## 8. Do you think that the assembly of the Haemoproteus takowskyi genome is a reasonable approximation of the true genome?
(CHECK)
I do not think its too bad, however we do have to keep in mind that only one other species had fewer BUSCO genes found. 

## 9. How many of the BUSCOs are found in all eight organisms?
(CHECK)
```bash=
#make one concatonated file
for file in *.faa; do cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv | grep -v "#" | grep -v "Missing"| grep -v "Fragmented" | cut -f 1 | sort -u  >>concat_full_table.tsv ;done

#find BUSCO genes found in all 8
cat concat_full_table.tsv | sort | uniq -c| cut -d " " -f 7 | grep 8 | wc -l
# 185
#now for 7 species
#one contatonated file
ls *.faa | grep -v Tg.faa | while read file; do cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv | grep -v "#" | grep -v "Missing"| grep -v "Fragmented" | cut -f 1 | sort -u  >>7org_full_table.tsv ;done
```
185 BUSCOs are found in all eight organisms.

## 10. If Toxoplasma is removed, how many BUSCOs are shared among the remaining seven species. Interpret!
```bash=
#now for 7 species
#one contatonated file
ls *.faa | grep -v Tg.faa | while read file; do cat ${file%.faa}/run_apicomplexa_odb10/full_table.tsv | grep -v "#" | grep -v "Missing"| grep -v "Fragmented" | cut -f 1 | sort -u  >>7org_full_table.tsv ;done

#find BUSCO found in all 7
cat 7org_full_table.tsv | sort | uniq -c| cut -d " " -f 7 | grep 7 | wc -l
204

```
204 BUSCOS are found in all seven organisms. If Taxoplasma is our outgroup, it makes sense that more BUSCO genes were found within the ingroup.

## 11. Does all protein trees reflect the “true’’ species tree?
![This is an image](https://github.com/adepennart/Malaria_Case_study/blob/main/gc_30.jpg)

As seen by the tree above, it is the same as 'true' species tree.

## 12. What is the phylogenetic position of Plasmodium falciparum?

Plasmodium falciparum is the most distantly related species of plasmodium, and the closest related plasmodium to Haemoproteus.
