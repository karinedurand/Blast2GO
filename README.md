# Blast2GO
Readme
# Blast2GO Input Preparation

## 1. InterProScan (Genotoul SLURM)
Run InterProScan to generate XML output for Blast2GO.

**Script**: `Test_interproscan.sh`
```bash
#!/bin/bash
#SBATCH -p workq
#SBATCH --job-name=InterProScan
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16G

module load devel/java/17.0.6 devel/python/Python-3.6.3
module load bioinfo/InterProScan/5.51-85.0

interproscan.sh \
  -mode cluster \
  -clusterrunid Test_cluster_$SLURM_JOBID \
  -i cnv.fa \
  -b cnv_$SLURM_JOBID \
  -f xml \
  -cpu 8 \
  -goterms \
  -iprlookup \
  -pa \
  -dp
```
- **Input**: `cnv.fa` (protein sequences in FASTA format)
- **Output**: `cnv_$SLURM_JOBID.xml`
- **Notes**: Ignore initial log errors; they do not affect results.

## 2. BLASTP (GenOuest SLURM)
Run BLASTP to generate XML output for Blast2GO.

**Script**: `blast.sh`
```bash
#!/bin/bash
source /local/env/envblast-2.16.0.sh

blastp -query cnv.fa \
       -db /db/nr/NR_2024-6-5/flat/nr \
       -out cnv_blastp_results.xml \
       -outfmt 5 \
       -evalue 1e-5 \
       -max_target_seqs 5 \
       -num_threads 8
```
- **Input**: `cnv.fa`
- **Output**: `cnv_blastp_results.xml`
- **Database**: `/db/nr/NR_2024-6-5/flat/nr` (updated 2024-06-06)

## 3. Blast2GO Import
- Import `cnv_$SLURM_JOBID.xml` (InterProScan) and `cnv_blastp_results.xml` (BLASTP) into Blast2GO for functional annotation.
- Ensure XML formats are compatible (outfmt 5 for BLASTP, XML for InterProScan).

## Requirements
- **Genotoul**: SLURM, InterProScan 5.51-85.0, Java 17.0.6, Python 3.6.3
- **GenOuest**: SLURM, BLAST+ 2.16.0
- Input sequences in FASTA format (`cnv.fa`)

## Notes
- Verify paths to input files and databases.
- Adjust memory (`--mem`) or CPUs (`--cpus-per-task`, `-num_threads`) based on cluster resources.
