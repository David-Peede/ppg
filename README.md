# ppg

Plasmodium Pop-Gen

## Preprocessing the _Plasmodium falciparum_ Community Project V7.0 Dataset

__[`Bgzip`](https://www.htslib.org/doc/bgzip.html) and [`Tabix`](https://www.htslib.org/doc/tabix.html)__

```bash
# Ungzip, bgzip, and create an index file.
for CHR in 01 02 03 04 05 06 07 08 09 10 11 12 13 14; do
sbatch -J Pf3D7_${CHR}_v3.pf.clean_vcf -N 1 -n 1 -t 1-0 --mem=2G -p batch --account=ccmb-condo -o logs/Pf3D7_${CHR}_v3.pf.clean_vcf-%A.out -e logs/Pf3D7_${CHR}_v3.pf.clean_vcf-%A.err --mail-type=FAIL --mail-user=david_peede@brown.edu --wrap="module load tabix; gunzip -d ./vcf_data/init_vcfs/Pf3D7_${CHR}_v3.pf7.vcf.gz; bgzip ./vcf_data/init_vcfs/Pf3D7_${CHR}_v3.pf7.vcf; tabix -p vcf ./vcf_data/init_vcfs/Pf3D7_${CHR}_v3.pf7.vcf.gz"
done
```

__Split SNV Records__

```bash
# Create new records for SNV variants at multiallelic sites.
for CHR in 01 02 03 04 05 06 07 08 09 10 11 12 13 14; do
sbatch -J Pf3D7_${CHR}_v3.pf.split_records -N 1 -n 1 -t 1-0 --mem=100G -p batch --account=ccmb-condo -o logs/Pf3D7_${CHR}_v3.pf.split_records-%A.out -e logs/Pf3D7_${CHR}_v3.pf.split_records-%A.err --mail-type=FAIL --mail-user=david_peede@brown.edu --wrap="module load bcftools; bcftools norm -m -snps -Oz -o ./vcf_data/init_vcfs/Pf3D7_${CHR}_v3.pf7.split_records.vcf.gz ./vcf_data/init_vcfs/Pf3D7_${CHR}_v3.pf7.vcf.gz"
done
```

## `vcfs`

`identify_duplicate_records.py`

- `sys.argv[1] = vcf_prefix`: VCF name up to the `.vcf.gz`; e.g., `Pf3D7_${CHR}_v3.pf7`.
- `sys.argv[2] = chrom`: Chromosome ID from the VCF for the regions file; e.g., `Pf3D7_${CHR}_v3`.
- `sys.argv[3] = vcf_path`: File path to the VCF `/users/dpeede/data/dpeede/13_plasmodium/vcf_data/init_vcfs`.
- `sys.argv[4] = dup_path`: File path to output the duplicate record region file; e.g, `/users/dpeede/data/dpeede/13_plasmodium/vcf_data/dup_info`.

```bash
# Example usage.
for CHR in 01 02 03 04 05 06 07 08 09 10 11 12 13 14; do
sbatch -J Pf3D7_${CHR}_v3.pf.identify_dups -N 1 -n 1 -t 1-0 --mem=2G -p batch --account=ccmb-condo -o logs/Pf3D7_${CHR}_v3.pf.identify_dups-%A.out -e logs/Pf3D7_${CHR}_v3.pf.identify_dups-%A.err --mail-type=FAIL --mail-user=david_peede@brown.edu --wrap="module load tabix python; python ./ppg/vcfs/identify_duplicate_records.py Pf3D7_${CHR}_v3.pf7 Pf3D7_${CHR}_v3 /users/dpeede/data/dpeede/13_plasmodium/vcf_data/init_vcfs /users/dpeede/data/dpeede/13_plasmodium/vcf_data/dup_info | bgzip > /users/dpeede/data/dpeede/13_plasmodium/vcf_data/dup_info/Pf3D7_${CHR}_v3.pf7.dup_records.vcf.gz"
done
```
