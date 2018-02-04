# Oneliners
**Oneliners for processing and analyzing genomics data.**

An excelent list by Stephen Turner can be found here: [https://github.com/stephenturner/oneliners]. Other rather random oneliners below. Use with caution, some of them were scrounged from all over the internet and modified as needed. Please report any bugs as issues or by email (only if you don't have a github account).

To count reads in a all .fastq.gz files in your current folder (fast, using gnu parallel):

```
parallel "echo {} && gunzip -c {} | wc -l | awk '{d=\$1; print d/4;}'" ::: *.gz
```
To count reads in a all .fastq.gz files in your current folder:

```
zcat *.gz | echo $((`wc -l`/4))
```
To count reads in a all .fastq files in your current folder:
```
cat *.fastq | echo $((`wc -l`/4))
```
To count base pairs in a all .fastq.gz files in your current folder:
```
zcat *.fastq.gz | paste - - - - | cut -f 2 | tr -d '\n' | wc -c
```
To gzip all your fastq files using gnu parallel and gzip:
```
parallel gzip ::: *.fastq
```
To gzip all your fastq files using pigz:
```
pigz *.fastq
```
To count all sequences in a fasta file:
```
grep "^>" yourfile.fasta -c
```
To count all sequences in all fasta files in your current directory:
```
for a in *.fasta; do ls $a; grep "^>" -c $a; done
```
To keep only one copy of duplicated lines:
```
awk '!seen[$0]++'
```
To sum assembly size from SPAdes contigs.fasta or scaffolds.fasta file:
```
grep "^>" scaffolds.fasta | cut -f 4 -d '_' | paste -sd+ | bc
```
To remove everything after the first space at each line, e.g. to to simplify fasta headers:
```
cut -d' ' -f1 < your_file
```
To split multifasta file into many fasta files:
```
awk '/^>/ {OUT=substr($0,2) ".fa"}; {print >> OUT; close(OUT)}' Input_File
```
To convert Illumina FASTQ 1.3 to 1.8:
```
sed -e '4~4y/@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\\]^_`abcdefghi/!"#$%&'\''()*+,-.\/0123456789:;<=>?@ABCDEFGHIJ/' f.fastq
```
To convert FASTQ to FASTA:
```
sed -n '1~4s/^@/>/p;2~4p'
```
To get fastq read length distribution:
```
cat reads.fastq | awk '{if(NR%4==2) print length($1)}' | sort | uniq -c
```
To deinterleave interleaved fastq file:
```
cat myf.fq | paste - - - - - - - - | tee >(cut -f 1-4 | tr "\t" "\n" > myfile_1.fq) | cut -f 5-8 | \
tr "\t" "\n" > myf2.fq
```
To filter and sort contig identifiers from SPAdes assembly (e.g. here lenght >= 4000 + coverage >=100):
```
grep "^>" scaffolds.fasta | sed s"/_/ /"g | awk '{ if ($4 >= 4000 && $6 >= 100) print $0 }' | sort -k 4 -n | \
sed s"/ /_/"g
```
To append something to all headers of your fasta files:
```
sed 's/>.*/&YOURSTRING/' filename.fasta > new_filename.fasta
```
To print difference between the last and first row in 5th column:
```
awk '{if (!first){first=$5;}; last=$5;} END {print last-first}' myfile.txt
```
To sample only 200 first bases from all sequences in a multifasta file (e.g. from assembly scaffolds.fasta file here):
```
awk '/^>/{ seqlen=0; print; next; } seqlen < 200 { if (seqlen + length($0) > 200) $0 = substr($0, 1, 200-seqlen);\
 seqlen += length($0); print }' scaffolds.fasta > 200bp_scaffolds.fasta
```
To remove all line ends (\n) from a Unix text file:
```
sed ':a;N;$!ba;s/\n//g' filename.txt > newfilename_oneline.txt
```
To get average for a column of numbers (here the second column $2):
```
awk '{ sum += $2; n++ } END { if (n > 0) print sum / n; }'
```
To get sequence length for all sequences in a fasta file:
```
awk '/^>/ {if (seqlen){print seqlen}; print ;seqlen=0;next; } { seqlen = seqlen +length($0)}END{print seqlen}' \
filename.fasta
```
To copy (move, rename, etc) files based on their list in a text file:
```
cat file_list.txt | while read line; do cp "$line" complete_dataset/"$line"; done
```
To split bam files into sets with mapped and unmapped reads:
```
samtools view -F4 sample.bam > sample.mapped.sam
samtools view -f4 sample.bam > sample.unmapped.sam
```
To pipe a compressed fasta file directly into makeblastdb.
```
gunzip -c fasta.gz | makeblastdb -in -
```
To remove sequences with duplicate fasta headers from a fasta file.
```
awk '/^>/{f=!d[$1];d[$1]=1}f' in.fasta > out.fasta
```
To replace/squeeze multiple adjacent spaces by only one space: 
```
tr -s " " < file
```
To filter fastq based on length (here larger than or equal to 21, but smaller than or equal to 25.
```
cat your.fastq | paste - - - - | awk 'length($2)  >= 21 && length($2) <= 25' | sed 's/\t/\n/g' > filtered.fastq
```
