# Read QC of HiSeq & MiSeq data
#### Notes from quality trimming illumina reads for assembly
```
sga preprocess -q 20 -f 20 -m 59 --pe-mode=1 ../Pb2_MiseqNextera/Pb2_MiseqNextera_R1.fastq ../Pb2_MiseqNextera/Pb2_MiseqNextera_R2.fastq > pb.miseq.sga.q20.fastq
sga preprocess -q 20 -f 20 -m 49 --pe-mode=1 Pb2_HiSeqStandardIllumina_1.fastq Pb2_HiSeqStandardIllumina_2.fastq > pb.hiseq.q20.fastq
```
-s necessary for valid fastq format output (no line wrapping)
```
tagdust -s Nextera_adapters.fasta pb.miseq.sga.q20.fastq -o pb.miseq.sga.q20.dusted.fastq
```
#### gettings as r1 and r2 separate files
```
grep -A3 "^@M01.*\?/1$" pb.miseq.sga.q20.dusted.fastq | grep -v "^--$"> r1.pb2.miseq.nexera.sgaq20.fastq
grep -A3 "^@M01.*\?/2$"  pb.miseq.sga.q20.dusted.fastq | grep -v "^--$" > r2.pb2.miseq.nexera.sgaq20.fastq

fastq-remove-orphans-EGW.pl -1 r1.pb.miseq.sga.q20.dusted.fastq -2 r2.pb.miseq.sga.q20.dusted.fastq
~/software/idba-1.1.0/bin/fq2fa --merge paired_r1.pb.miseq.sga.q20.dusted.fastq paired_r2.pb.miseq.sga.q20.dusted.fastq pb.miseq.sga.q20.dusted.interleaved.fasta

mv pb.miseq.sga.q20.dusted.interleaved.250bpMAX.fasta miseqNex.Pb2.250bp.QCi.fasta
```

# Co-assembly of HiSeq & Miseq with idba_ud = hyb1 assembly
~/software/idba-1.1.0_hack2/idba-1.1.0/bin/idba_ud -r miseq.hiseq.QC.i.fasta -o hyb.1 --num_threads 16 --min_contig 500 --maxk 250
