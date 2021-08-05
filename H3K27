
#!/bin/sh
#$ -N H3K27A
#$ -cwd
#$ -l h_rt=48:00:00
#$ -l h_vmem=30G


. /etc/profile.d/modules.sh

module load igmm/apps/python/2.7.10
module load igmm/apps/bowtie/2.2.5
module load igmm/libs/ncurses/6.0
module load igmm/apps/samtools/1.3
module load java/jdk/1.8.0
module load igmm/apps/BEDTools/2.23.0
module load igmm/apps/FastQC/0.11.9

fastqc "fastq_files"*

bowtie2  --local --very-sensitive-local --no-unal --no-mixed --no-discordant --phred33 -I 10 -X 700 -x mm9 -1 "fatstq_forward" -2 "fastq_reverse" -S H3K27A_paired_align.sam

awk '$3!="chrM" && $3!="*" {print}' H3K27A_paired_align.sam > H3K27A_paired_align_subMitoUnc.sam

samtools view -bS H3K27A_paired_align_subMitoUnc.sam > H3K27A_paired_align_subMitoUnc.bam

samtools sort H3K27A_paired_align_subMitoUnc.bam -o H3K27A_paired_align_subMitoUnc_sorted.bam

java -jar picard.jar MarkDuplicates I=H3K27A_paired_align_subMitoUnc_sorted.bam O=H3K27A_paired_align_subMitoUnc_nonDupl.bam M=H3K27A_metrics.txt REMOVE_DUPLICATES=true 

bedtools bamtobed -i H3K27A_paired_align_subMitoUnc_nonDupl.bam > H3K27A_paired_align_sorted_filtered.bed

samtools sort H3K27A_paired_align_subMitoUnc_nonDupl.bam > H3K27A_paired_align_subMitoUnc_nonDupl_sorted.bam

samtools index H3K27A_paired_align_subMitoUnc_nonDupl_sorted.bam H3K27A_paired_align_subMitoUnc_nonDupl_sorted.bam.bai

bedtools genomecov -ibam H3K27A_paired_align_subMitoUnc_nonDupl_sorted.bam -bg -split -g mm9.chr.sizes > H3K27A_paired_align_final.bedGraph

LC_COLLATE=C sort -k1,1 -k2,2n H3K27A_paired_align_final.bedGraph > H3K27A_paired_align_final_sorted.bedGraph

bedGraphToBigWig H3K27A_paired_align_final_sorted.bedGraph mm9.chr.sizes H3K27A_paired_align.bw
