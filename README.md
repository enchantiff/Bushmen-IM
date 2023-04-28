# JuHoan-IM
Scripts used to analyse temporal changes to the intestinal microbiome of the Ju’|hoansi bushmen of Namibia. See flow diagram below:

## QIIME2
QIIME2 pipeline (ref) used to produce the IM data.

### Importing into QIIME2
`qiime tools import –type SampleData[PairedEndSequencesWithQuality] --input-path /nlustre/users/tiffdp/africanim/2019_samples/manifest.txt –-output-path /nlustre/users/tiffdp/africanim/2019_samples/2019_demux.qza –-input-format PairedEndFastqManifestPhred33V2`

`qiime demux summarize --i-data /nlustre/users/tiffdp/africanim/2019_samples/2019_demux.qza --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019_demux.qzv`

### Denoising and Demultiplexing
`qiime dada2 denoise-paired --i-demultiplexed-seqs /nlustre/users/tiffdp/africanim/2019_samples/2019_demux.qza --p-trim-left-f 26 --p-trim-left-r 24 --p-trunc-len-f 250 --p-trunc-len-r 250 --p-n-threads 0 --o-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --o-representative-sequences /nlustre/users/tiffdp/africanim/2019_samples/2019_rep_seqs.qza --o-denoising-stats /nlustre/users/tiffdp/africanim/2019_samples/2019_stats.qza`

`qiime metadata tabulate --m-input-file /nlustre/users/tiffdp/africanim/2019_samples/2019_stats.qza --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019_stats.qzv`

### Feature Table
`qiime feature-table summarize --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qzv --m-sample-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt`

`qiime feature-table tabulate-seqs --i-data /nlustre/users/tiffdp/africanim/2019_samples/2019_rep_seqs.qza --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019_rep_seqs.qzv`

### Phylogeny
`qiime phylogeny align-to-tree-mafft-fasttree --i-sequences /nlustre/users/tiffdp/africanim/2019_samples/2019_rep_seqs.qza --o-alignment /nlustre/users/tiffdp/africanim/2019_samples/2019p_aligned_rep_seqs.qza --o-masked-alignment /nlustre/users/tiffdp/africanim/2019_samples/2019p_masked_aligned_rep_seqs.qza --o-tree /nlustre/users/tiffdp/africanim/2019_samples/2019p_unrooted_tree.qza --o-rooted-tree /nlustre/users/tiffdp/africanim/2019_samples/2019p_rooted_tree.qza`

### Alpha Rarefaction
`qiime diversity alpha-rarefaction --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --i-phylogeny /nlustre/users/tiffdp/africanim/2019_samples/2019p_rooted_tree.qza --p-max-depth 90520 --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019p_alpha_rarefaction.qzv --verbose`

### Diversity Indicies
`qiime diversity core-metrics-phylogenetic --i-phylogeny /nlustre/users/tiffdp/africanim/2021_samples/2021p_rooted_tree.qza --i-table /nlustre/users/tiffdp/africanim/2021_samples/2021_table.qza --p-sampling-depth 5538 --m-metadata-file /nlustre/users/tiffdp/africanim/2021_samples/2021_metadata.txt --output-dir /nlustre/users/tiffdp/africanim/2021_samples/2021p_core_metrics_results`

`qiime diversity alpha-group-significance --i-alpha-diversity /nlustre/users/tiffdp/africanim/2021_samples/2021p_core_metrics_results/faith_pd_vector.qza --m-metadata-file /nlustre/users/tiffdp/africanim/2021_samples/2021_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2021_samples/2021p_core_metrics_results/faith_pd_group_significance.qzv`

`qiime diversity alpha-group-significance --i-alpha-diversity /nlustre/users/tiffdp/africanim/2021_samples/2021p_core_metrics_results/evenness_vector.qza --m-metadata-file /nlustre/users/tiffdp/africanim/2021_samples/2021_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2021_samples/2021p_core_metrics_results/evenness_group_significance.qzv`

### Taxonomic Classification
`qiime feature-classifier extract-reads --i-sequences /nlustre/users/tiffdp/africanim/silva-138-99-seqs.qza --p-f-primer GACTCCTACGGGAGGCWGCAG --p-r-primer GACTACHVGGGTATCTAATCC --p-min-length 100 --p-max-length 400 --o-reads /nlustre/users/tiffdp/africanim/silva_16S_extracted_reads.qza`

`qiime feature-classifier fit-classifier-naive-bayes --i-reference-reads /nlustre/users/tiffdp/africanim/silva_16S_extracted_reads.qza --i-reference-taxonomy /nlustre/users/tiffdp/africanim/silva-138-99-tax.qza --o-classifier /nlustre/users/tiffdp/africanim/classifier-silva138-trained.qza`

`qiime feature-classifier classify-sklearn --i-classifier /nlustre/users/tiffdp/africanim/classifier-silva138-trained.qza --i-reads /nlustre/users/tiffdp/africanim/2019_samples/2019_rep_seqs.qza --o-classification /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxonomy.qza`

`qiime metadata tabulate --m-input-file /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxonomy.qza --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxonomy.qzv`

`qiime taxa barplot --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --i-taxonomy /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxonomy.qza --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxa_bar_plots.qzv`

`qiime taxa collapse --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --i-taxonomy /nlustre/users/tiffdp/africanim/2019_samples/2019pt_taxonomy.qza --p-level 7 --o-collapsed-table /nlustre/users/tiffdp/africanim/2019_samples/2019pt_phyla_table.qza`

### Relative Frequency
`qiime feature-table relative-frequency --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019pt_phyla_table.qza --o-relative-frequency-table /nlustre/users/tiffdp/africanim/2019_samples/2019pt_rel_phyla_table.qza`

`qiime tools export --input-path /nlustre/users/tiffdp/africanim/2019_samples/2019pt_rel_phyla_table.qza --output-path /nlustre/users/tiffdp/africanim/2019_samples/relphylapt`

`biom convert -i /nlustre/users/tiffdp/africanim/2019_samples/relphylapt/feature-table.biom -o /nlustre/users/tiffdp/africanim/2019_samples/relphylapt/2019pt_rel-phyla-table.tsv --to-tsv`

### IQ Tree
`qiime phylogeny iqtree --i-alignment /nlustre/users/tiffdp/africanim/2019_samples/2019p_masked_aligned_rep_seqs.qza --o-tree /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_tree.qza –-verbose`

`qiime phylogeny midpoint-root --i-tree /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_tree.qza --o-rooted-tree /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_rooted_tree.qza`

### Alpha Rarefaction
`qiime diversity alpha-rarefaction --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --i-phylogeny /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_rooted_tree.qza --p-max-depth 90520 --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019p_alpha_rarefaction.qzv --verbose`

### Diversity Indicies
`qiime diversity core-metrics-phylogenetic --i-phylogeny /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_rooted_tree.qza --i-table /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --p-sampling-depth 5538 --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --output-dir /nlustre/users/tiffdp/africanim/2019_samples/2019p_core_metrics_results`

`qiime diversity alpha-group-significance --i-alpha-diversity /nlustre/users/tiffdp/africanim/2019_samples/2019p_core_metrics_results/faith_pd_vector.qza --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019p_core_metrics_results/faith_pd_group_significance.qzv`

`qiime diversity alpha-group-significance --i-alpha-diversity /nlustre/users/tiffdp/africanim/2019_samples/2019p_core_metrics_results/evenness_vector.qza --m-metadata-file /nlustre/users/tiffdp/africanim/2019_samples/2019_metadata.txt --o-visualization /nlustre/users/tiffdp/africanim/2019_samples/2019p_core_metrics_results/evenness_group_significance.qzv`

### Biom Table
`qiime tools export --input-path /nlustre/users/tiffdp/africanim/2019_samples/2019_table.qza --output-path /nlustre/users/tiffdp/africanim/2019_samples/2019_feature_table`

`biom convert --to-tsv -i /nlustre/users/tiffdp/africanim/2019_samples/2019_feature_table/feature-table.biom -o /nlustre/users/tiffdp/africanim/2019_samples/2019_feature_table/2019_table.tsv`

### Exported Tree
`qiime tools export --input-path /nlustre/users/tiffdp/africanim/2019_samples/iqt19p_rooted_tree.qza --output-path /nlustre/users/tiffdp/africanim/2019_samples/19_exported_tree`
