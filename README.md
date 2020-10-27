#### Installation
* `git clone https://github.com/nanoporetech/tombo`
* `cd tombo`
* `pip install -e .`

## Primer Extension

#### Preprocess
* `tombo preprocess annotate_raw_with_fastqs --fast5-basedir /brdu_controls_from_muller/primer_extension/fast5/ --fastq-filenames /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq --processes 15`
* Took about 30mins

#### Re-squiggle
* `tombo resquiggle /brdu_controls_from_muller/primer_extension/fast5/ /brdu_controls_from_muller/primer_extension/ref.fa --processes 15 --num-most-common-errors 5`
* For some reason the alignments are not being produced....
```[22:38:05] Loading minimap2 reference.
   [22:38:05] Getting file list.
   [22:38:13] Loading default canonical ***** DNA ***** model.
   [22:38:14] Re-squiggling reads (raw signal to genomic sequence alignment).
   5 most common unsuccessful read types (approx. %):
       97.4% (3088196 reads) : Alignment not produced
        1.6% (  50302 reads) : Tombo data exists in [--corrected-group] and [--overwrite] is not set
        0.6% (  17890 reads) : Fastq slot not present in --basecall-group
        0.0% (   1260 reads) : Poor raw to expected signal matching (revert with `tombo filter clear_filters`)
        0.0% (     18 reads) : Read event to sequence alignment extends beyond bandwidth
   100%|██████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 3173118/3173118 [4:40:29<00:00, 188.55it/s]
   ******************** WARNING ********************
           Unexpected errors occured. See full error stack traces for first (up to) 50 errors in "unexpected_tombo_errors.9441.err"
   [03:18:43] Final unsuccessful reads summary (99.5% reads unsuccessfully processed; 3157697 total reads):
       97.3% (3088226 reads) : Alignment not produced
        1.6% (  50302 reads) : Tombo data exists in [--corrected-group] and [--overwrite] is not set
        0.6% (  17890 reads) : Fastq slot not present in --basecall-group
        0.0% (   1260 reads) : Poor raw to expected signal matching (revert with `tombo filter clear_filters`)
        0.0% (     18 reads) : Read event to sequence alignment extends beyond bandwidth
        0.0% (      1 reads) : Unexpected error
   [03:18:43] Saving Tombo reads index to file.
```

#### De-novo detection
* `tombo detect_modifications de_novo --fast5-basedirs /brdu_controls_from_muller/primer_extension/fast5/ --statistics-file-basename primer_extension.de_novo --per-read-statistics-basename per_read.primer_extension.de_novo --processes 15 --minimum-test-reads 100`

#### Text output
* `tombo text_output signif_sequence_context --statistics-filename /brdu_controls_from_muller/primer_extension/tombo_output/primer_extension.de_novo.tombo.stats --genome-fasta /brdu_controls_from_muller/primer_extension/ref.fa --fast5-basedirs /brdu_controls_from_muller/primer_extension/fast5/`

#### Plotting
* `tombo plot most_significant --overplot-type Density --fast5-basedirs /brdu_controls_from_muller/primer_extension/fast5/ --statistics-filename /brdu_controls_from_muller/primer_extension/tombo_output/primer_extension.de_novo.tombo.stats --plot-standard-model`
* `tombo plot genome_locations --genome-locations FdU:37:+ IdU:37:+ BrdU:37:+ EdU:37:+ T:37:+ --overplot-type Density --fast5-basedirs  /brdu_controls_from_muller/primer_extension/fast5/ --plot-standard-model`

### Sample Compare method
* Split Reads
    * `bamtools split -in /brdu_controls_from_muller/primer_extension/fastq/split_alignments/primer_extension.bam -reference`
    * `samtools view primer_extension.REF_FdU.bam |  awk '{ print $1 }' > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/FdUids.txt`
    * `samtools view primer_extension.REF_BrdU.bam | awk '{ print $1 }' > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/BrdUids.txt`
    * `samtools view primer_extension.REF_EdU.bam | awk '{ print $1 }' > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/EdUids.txt`
    * `samtools view primer_extension.REF_IdU.bam | awk '{ print $1 }' > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/IdUids.txt`
    * `samtools view primer_extension.REF_T.bam | awk '{ print $1 }' > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/Tids.txt`
    * `cat /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq.index.readdb | grep -f /brdu_controls_from_muller/primer_extension/fastq/split_alignments/Tids.txt > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/Tids.readdb`
    * `cat /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq.index.readdb | grep -f /brdu_controls_from_muller/primer_extension/fastq/split_alignments/IdUids.txt > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/IdUids.readdb`
    * `cat /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq.index.readdb | grep -f /brdu_controls_from_muller/primer_extension/fastq/split_alignments/EdUids.txt > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/EdUids.readdb`
    * `cat /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq.index.readdb | grep -f /brdu_controls_from_muller/primer_extension/fastq/split_alignments/BrdUids.txt > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/BrdUids.readdb`
    * `cat /brdu_controls_from_muller/primer_extension/fastq/2017_06_05_CAM_ONT_BrdU_PCRs_primer_ext_basecalled_guppy3.1.5.fastq.index.readdb | grep -f /brdu_controls_from_muller/primer_extension/fastq/split_alignments/FdUids.txt > /brdu_controls_from_muller/primer_extension/fastq/split_alignments/FdUids.readdb`
    * `awk '{ print $2 }' BrdUids.readdb  | xargs -i cp {} fast5s/BrdU/`
    * `awk '{ print $2 }' EdUids.readdb  | xargs -i cp {} fast5s/EdU/`
    * `awk '{ print $2 }' FdUids.readdb  | xargs -i cp {} fast5s/FdU/`
    * `awk '{ print $2 }' IdUids.readdb  | xargs -i cp {} fast5s/IdU/`
    * `awk '{ print $2 }' Tids.readdb  | xargs -i cp {} fast5s/T/`

* Create Fastqs
    * `/brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs`

* Run Tombo
    * Re-squiggle
        * `tombo resquiggle /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/T.fa --processes 15 --num-most-common-errors 5 --overwrite`
        * `tombo resquiggle /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/BrdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/BrdU.fa --processes 15 --num-most-common-errors 5 --overwrite`
        * `tombo resquiggle /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/EdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/EdU.fa --processes 15 --num-most-common-errors 5 --overwrite`
        * `tombo resquiggle /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/FdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/FdU.fa --processes 15 --num-most-common-errors 5 --overwrite`
        * `tombo resquiggle /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/IdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/IdU.fa --processes 15 --num-most-common-errors 5 --overwrite`

* De-novo detection
    * `tombo detect_modifications level_sample_compare --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T  \
        --alternate-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/BrdU \
        --statistics-file-basename BrdU_vs_T.level_compare_sample`
    * `tombo detect_modifications level_sample_compare --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T  \
        --alternate-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/EdU \
        --statistics-file-basename EdU_vs_T.level_compare_sample`
    * `tombo detect_modifications level_sample_compare --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T  \
        --alternate-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/FdU \
        --statistics-file-basename FdU_vs_T.level_compare_sample`
    * `tombo detect_modifications level_sample_compare --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T  \
        --alternate-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/IdU \
        --statistics-file-basename IdU_vs_T.level_compare_sample`
                
        
* Text Output
    * `tombo text_output signif_sequence_context --statistics-filename BrdU_vs_T.level_compare_sample.tombo.stats --genome-fasta /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/T.fa --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/BrdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T --sequences-filename BrdU_vs_T.fasta`
    * `tombo text_output signif_sequence_context --statistics-filename EdU_vs_T.level_compare_sample.tombo.stats --genome-fasta /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/T.fa --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/EdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T --sequences-filename EdU_vs_T.fasta`
    * `tombo text_output signif_sequence_context --statistics-filename FdU_vs_T.level_compare_sample.tombo.stats --genome-fasta /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/T.fa --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/FdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T --sequences-filename FdU_vs_T.fasta`
    * `tombo text_output signif_sequence_context --statistics-filename IdU_vs_T.level_compare_sample.tombo.stats --genome-fasta /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fastqs/T.fa --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/IdU /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T --sequences-filename IdU_vs_T.fasta`

* Plot Output
    * `tombo plot genome_locations --pdf-filename BrdU_vs_T.pdf --genome-locations primer_extension:37:+ --overplot-type Density --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/BrdU --control-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T`
    * `tombo plot genome_locations --pdf-filename EdU_vs_T.pdf --genome-locations primer_extension:37:+ --overplot-type Density --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/EdU --control-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T`
    * `tombo plot genome_locations --pdf-filename FdU_vs_T.pdf --genome-locations primer_extension:37:+ --overplot-type Density --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/FdU --control-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T`
    * `tombo plot genome_locations --pdf-filename IdU_vs_T.pdf --genome-locations primer_extension:37:+ --overplot-type Density --fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/IdU --control-fast5-basedirs /brdu_controls_from_muller/primer_extension/fastq/split_alignments/fast5s/T`

## rRNA
#### Preprocess
* Upload the data to S3 `bailey-16s-rrna/fast5/*` and downloaded data to ModificationDetection `~/rrna_16s/fast5`

#### Re-squiggle
* `tombo resquiggle /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native/ /home/ubuntu/rrna_16s/reference/ec_16S.fa --processes 12 --num-most-common-errors 5 --rna --overwrite`
* `tombo resquiggle /home/ubuntu/rrna_16s/fast5/02_25_17_R9.4_16S_Ec_Psi516/ /home/ubuntu/rrna_16s/reference/ec_16S.fa --processes 12 --num-most-common-errors 5 --rna --overwrite`
* `tombo resquiggle /home/ubuntu/rrna_16s/fast5/01_09_17_R9.4_16S_Ecoli_rsmG_def/ /home/ubuntu/rrna_16s/reference/ec_16S.fa --processes 12 --num-most-common-errors 5 --rna --overwrite`

#### De-novo detection
* `tombo detect_modifications level_sample_compare --fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native  \
    --alternate-fast5-basedirs /home/ubuntu/rrna_16s/fast5/02_25_17_R9.4_16S_Ec_Psi516 \
    --statistics-file-basename native_vs_psi.level_compare_sample`
* `tombo detect_modifications level_sample_compare --fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native  \
      --alternate-fast5-basedirs /home/ubuntu/rrna_16s/fast5/01_09_17_R9.4_16S_Ecoli_rsmG_def \
      --statistics-file-basename native_vs_m7G.level_compare_sample --fishers-method-context 3 --processes 15` 
     
#### Text output
* `tombo text_output signif_sequence_context --statistics-filename native_vs_m7G.level_compare_sample.tombo.stats --genome-fasta /home/ubuntu/rrna_16s/reference/ec_16S.fa --fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native /home/ubuntu/rrna_16s/fast5/01_09_17_R9.4_16S_Ecoli_rsmG_def`
* `tombo text_output signif_sequence_context --statistics-filename native_vs_psi.level_compare_sample.tombo.stats --genome-fasta /home/ubuntu/rrna_16s/reference/ec_16S.fa --fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native /home/ubuntu/rrna_16s/fast5/02_25_17_R9.4_16S_Ec_Psi516`

#### Plotting
* `tombo plot genome_locations  --overplot-type Density --genome-locations ecoli_MRE600:520:+ --control-fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native --fast5-basedirs /home/ubuntu/rrna_16s/fast5/02_25_17_R9.4_16S_Ec_Psi516`
* `tombo plot genome_locations  --overplot-type Density --genome-locations ecoli_MRE600:520:+ --control-fast5-basedirs /home/ubuntu/rrna_16s/fast5/03_17_17_R9.4_16S_Ec_native --fast5-basedirs /home/ubuntu/rrna_16s/fast5/01_09_17_R9.4_16S_Ecoli_rsmG_def`





