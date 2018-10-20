---
source: Rmd
title: "Annotation of genome bins. Week 2-3"
teaching: 20
exercises: 40
questions:
- "How to annotate genes in a set of contigs?"
- "How can I perform the same actions on many different files?"
objectives:
- "Further develop your genome assembly workflow"
- "Identify databases that could serve as sources of taxonomic and functional information for your sequences"
- "Learn how to repeat commands and processes tens, hundreds or thousands of files using loops"
- "Write a loop that applies one or more commands separately to each file in a set of files."
- "Use an automated genome annotation pipeline 'PROKKA'"
keypoints:
- "Loops are very useful for repeating commands over multiple files"
- "A `for` loop repeats commands once for every thing in a list."
- "Use `$name` to expand a variable (i.e., get its value). `${name}` can also be used."
- "Do not use spaces, quotes, or wildcard characters such as '*' or '?' in filenames, as it complicates variable expansion."
- "Give files consistent names that are easy to match with wildcard patterns to make it easy to select them for looping."
- "Use the up-arrow key to scroll up through previous commands to edit and repeat them."
- "Use `Ctrl-R` to search through the previously entered commands."
- "Use `history` to display recent commands, and `!number` to repeat a command by number."

---



## Completed Metagenome assembly, illumina Paired end sequences, Metabat bins

We have limitless options for annotating our metagenome, and metagenome bins. The standard
workflow involves
1. predict rRNA and tRNA
2. predict open reading frames
3. compare open reading frames against a database, or databases
  - extract functional annotations by homology
  - classify or assign taxonomic information using Last Common Ancestor or similar


To kick start the annotation we are going to use [PROKKA](https://github.com/tseemann/prokka) written an maintained by
Torsten Seeman
Seemann T.
Prokka: rapid prokaryotic genome annotation
Bioinformatics 2014 Jul 15;30(14):2068-9. PMID:24642063

What does [PROKKA do?](https://github.com/tseemann/prokka)

~~~
prokka
~~~
{: .language-bash}

~~~
prokka
Name:
  Prokka 1.13.3 by Torsten Seemann <torsten.seemann@gmail.com>
Synopsis:
  rapid bacterial genome annotation
Usage:
  prokka [options] <contigs.fasta>
General:
  --help             This help
  --version          Print version and exit
  --docs             Show full manual/documentation
  --citation         Print citation for referencing Prokka
  --quiet            No screen output (default OFF)
  --debug            Debug mode: keep all temporary files (default OFF)
Setup:
  --dbdir [X]        Prokka database root folders (default '/disks/jen/data/db/prokka_db')
  --listdb           List all configured databases
  --setupdb          Index all installed databases
  --cleandb          Remove all database indices
  --depends          List all software dependencies
Outputs:
  --outdir [X]       Output folder [auto] (default '')
  --force            Force overwriting existing output folder (default OFF)
  --prefix [X]       Filename output prefix [auto] (default '')
  --addgenes         Add 'gene' features for each 'CDS' feature (default OFF)
  --addmrna          Add 'mRNA' features for each 'CDS' feature (default OFF)
  --locustag [X]     Locus tag prefix [auto] (default '')
  --increment [N]    Locus tag counter increment (default '1')
  --gffver [N]       GFF version (default '3')
  --compliant        Force Genbank/ENA/DDJB compliance: --addgenes --mincontiglen 200 --centre XXX (default OFF)
  --centre [X]       Sequencing centre ID. (default '')
  --accver [N]       Version to put in Genbank file (default '1')
Organism details:
  --genus [X]        Genus name (default 'Genus')
  --species [X]      Species name (default 'species')
  --strain [X]       Strain name (default 'strain')
  --plasmid [X]      Plasmid name or identifier (default '')
Annotations:
  --kingdom [X]      Annotation mode: Archaea|Bacteria|Bacteria|Bacteria|Mitochondria|Viruses (default 'Bacteria')
  --gcode [N]        Genetic code / Translation table (set if --kingdom is set) (default '0')
  --gram [X]         Gram: -/neg +/pos (default '')
  --usegenus         Use genus-specific BLAST databases (needs --genus) (default OFF)
  --proteins [X]     FASTA or GBK file to use as 1st priority (default '')
  --hmms [X]         Trusted HMM to first annotate from (default '')
  --metagenome       Improve gene predictions for highly fragmented genomes (default OFF)
  --rawproduct       Do not clean up /product annotation (default OFF)
  --cdsrnaolap       Allow [tr]RNA to overlap CDS (default OFF)
Matching:
  --evalue [n.n]     Similarity e-value cut-off (default '1e-09')
  --coverage [n.n]   Minimum coverage on query protein (default '80')
Computation:
  --cpus [N]         Number of CPUs to use [0=all] (default '8')
  --fast             Fast mode - only use basic BLASTP databases (default OFF)
  --noanno           For CDS just set /product="unannotated protein" (default OFF)
  --mincontiglen [N] Minimum contig size [NCBI needs 200] (default '1')
  --rfam             Enable searching for ncRNAs with Infernal+Rfam (SLOW!) (default '0')
  --norrna           Don't run rRNA search (default OFF)
  --notrna           Don't run tRNA search (default OFF)
  --rnammer          Prefer RNAmmer over Barrnap for rRNA prediction (default OFF)
~~~
{: .output}

Lets try prokka on your favorite metabat bin
~~~

/usr/local/anaconda2/bin/prokka bin.1.fa -o bin.1.prokka.out
~~~
{: .language-bash}

~~~
[21:49:51] This is prokka 1.13.3
[21:49:51] Written by Torsten Seemann <torsten.seemann@gmail.com>
[21:49:51] Homepage is https://github.com/tseemann/prokka
[21:49:51] Local time is Sat Oct 20 21:49:51 2018
[21:49:51] You are ndebono
[21:49:51] Operating system is linux
[21:49:51] You have BioPerl 1.007002
[21:49:51] System has 30 cores.
[21:49:51] Will use maximum of 8 cores.
[21:49:51] Annotating as >>> Bacteria <<<
[21:49:51] Generating locus_tag from 'bin.1.fasta' contents.
[21:49:51] Setting --locustag EPFKBDBI from MD5 e9f4bdb2f5963fdabe8b08d5cdd4997f
[21:49:51] Creating new output folder: /disks/jen/data/mres/ndebono/bin.1.prokka.out
[21:49:51] Running: mkdir -p \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out
[21:49:51] Using filename prefix: PROKKA_10202018.XXX
[21:49:51] Setting HMMER_NCPU=1
[21:49:51] Writing log to: /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.log
[21:49:51] Command: /usr/local/anaconda2/bin/prokka bin.1.fasta -o /disks/jen/data/mres/ndebono/bin.1.prokka.out
[21:49:51] Appending to PATH: /usr/local/anaconda2/bin
[21:49:51] Looking for 'aragorn' - found /usr/local/anaconda2/bin/aragorn
[21:49:51] Determined aragorn version is 1.2
[21:49:51] Looking for 'barrnap' - found /usr/local/anaconda2/bin/barrnap
[21:49:51] Determined barrnap version is 0.9
[21:49:51] Looking for 'blastp' - found /usr/local/anaconda2/bin/blastp
[21:49:53] Determined blastp version is 2.7
[21:49:53] Looking for 'cmpress' - found /usr/local/anaconda2/bin/cmpress
[21:49:53] Determined cmpress version is 1.1
[21:49:53] Looking for 'cmscan' - found /usr/local/anaconda2/bin/cmscan
[21:49:54] Determined cmscan version is 1.1
[21:49:54] Looking for 'egrep' - found /bin/egrep
[21:49:54] Looking for 'find' - found /usr/bin/find
[21:49:54] Looking for 'grep' - found /bin/grep
[21:49:54] Looking for 'hmmpress' - found /usr/local/anaconda2/bin/hmmpress
[21:49:54] Determined hmmpress version is 3.2
[21:49:54] Looking for 'hmmscan' - found /usr/local/anaconda2/bin/hmmscan
[21:49:54] Determined hmmscan version is 3.2
[21:49:54] Looking for 'java' - found /usr/local/anaconda2/bin/java
[21:49:54] Looking for 'less' - found /usr/bin/less
[21:49:54] Looking for 'makeblastdb' - found /usr/local/anaconda2/bin/makeblastdb
[21:49:54] Determined makeblastdb version is 2.7
[21:49:54] Looking for 'minced' - found /usr/local/anaconda2/bin/minced
[21:49:54] Determined minced version is 3.0
[21:49:54] Looking for 'parallel' - found /usr/local/anaconda2/bin/parallel
[21:49:55] Determined parallel version is 20180922
[21:49:55] Looking for 'prodigal' - found /usr/local/anaconda2/bin/prodigal
[21:49:55] Determined prodigal version is 2.6
[21:49:55] Looking for 'prokka-genbank_to_fasta_db' - found /usr/local/anaconda2/bin/prokka-genbank_to_fasta_db
[21:49:55] Looking for 'sed' - found /bin/sed
[21:49:55] Looking for 'tbl2asn' - found /usr/local/anaconda2/bin/tbl2asn
[21:49:55] Determined tbl2asn version is 25.6
[21:49:55] Using genetic code table 11.
[21:49:55] Loading and checking input file: bin.1.fasta
[21:49:55] Wrote 285 contigs totalling 2014340 bp.
[21:49:55] Predicting tRNAs and tmRNAs
[21:49:55] Running: aragorn -l -gc11  -w \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/PROKKA_10202018\.fna
[21:49:56] 1 tRNA-His c[3772,3847] 34 (gtg)
[21:49:56] 2 tRNA-Arg c[3931,4007] 35 (tct)
[21:49:56] 1 tRNA-Phe [15375,15450] 34 (gaa)
[21:49:56] 1 tRNA-Asn c[3950,4025] 34 (gtt)
[21:49:56] 1 tRNA-SeC [4866,4958] 36 (tca)
[21:49:56] 1 tRNA-Val [7549,7624] 34 (tac)
[21:49:56] 2 tRNA-Asp [7669,7745] 35 (gtc)
[21:49:56] 1 tRNA-Cys c[6053,6126] 33 (gca)
[21:49:56] 1 tRNA-Lys c[5106,5181] 34 (ttt)
[21:49:56] 1 tRNA-Arg [15045,15120] 35 (cct)
[21:49:56] 1 tRNA-Gly [8092,8167] 34 (gcc)
[21:49:56] 1 tRNA-Ala c[6062,6137] 34 (ggc)
[21:49:56] 1 tRNA-Met c[4191,4267] 35 (cat)
[21:49:56] 2 tRNA-Leu c[4336,4422] 35 (gag)
[21:49:56] 1 tRNA-Met [2326,2402] 35 (cat)
[21:49:56] 1 tRNA-Gln c[2699,2773] 33 (ttg)
[21:49:56] 1 tRNA-Arg [10934,11010] 35 (tcg)
[21:49:56] Found 17 tRNAs
[21:49:56] Predicting Ribosomal RNAs
[21:49:56] Running Barrnap with 8 threads
[21:49:58] Found 0 rRNAs
[21:49:58] Skipping ncRNA search, enable with --rfam if desired.
[21:49:58] Total of 17 tRNA + rRNA features
[21:49:58] Searching for CRISPR repeats
[21:49:58] Found 0 CRISPRs
[21:49:58] Predicting coding sequences
[21:49:58] Contigs total 2014340 bp, so using single mode
[21:49:58] Running: prodigal -i \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/PROKKA_10202018\.fna -c -m -g 11 -p single -f sco -q
[21:50:00] Excluding CDS which overlaps existing RNA (tRNA) at k141_353983:4132..4371 on - strand
[21:50:00] Found 1884 CDS
[21:50:00] Connecting features back to sequences
[21:50:00] Not using genus-specific database. Try --usegenus to enable it.
[21:50:00] Annotating CDS, please be patient.
[21:50:00] Will use 8 CPUs for similarity searching.
[21:50:01] There are still 1884 unannotated CDS left (started with 1884)
[21:50:01] Will use blast to search against /disks/jen/data/db/prokka_db/kingdom/Bacteria/IS with 8 CPUs
[21:50:01] Running: cat \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/IS\.faa | parallel --gnu --plain -j 8 --block 35837 --recstart '>' --pipe blastp -query - -db /disks/jen/data/db/prokka_db/kingdom/Bacteria/IS -evalue 1e-30 -qcov_hsp_perc 90 -num_threads 1 -num_descriptions 1 -num_alignments 1 -seg no > \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/IS\.blast 2> /dev/null
[21:50:06] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/IS.faa
[21:50:06] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/IS.blast
[21:50:07] There are still 1884 unannotated CDS left (started with 1884)
[21:50:07] Will use blast to search against /disks/jen/data/db/prokka_db/kingdom/Bacteria/AMR with 8 CPUs
[21:50:07] Running: cat \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/AMR\.faa | parallel --gnu --plain -j 8 --block 35837 --recstart '>' --pipe blastp -query - -db /disks/jen/data/db/prokka_db/kingdom/Bacteria/AMR -evalue 9.99999999999999e-301 -qcov_hsp_perc 90 -num_threads 1 -num_descriptions 1 -num_alignments 1 -seg no > \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/AMR\.blast 2> /dev/null
[21:50:11] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/AMR.faa
[21:50:11] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/AMR.blast
[21:50:11] There are still 1884 unannotated CDS left (started with 1884)
[21:50:11] Will use blast to search against /disks/jen/data/db/prokka_db/kingdom/Bacteria/sprot with 8 CPUs
[21:50:11] Running: cat \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/sprot\.faa | parallel --gnu --plain -j 8 --block 35837 --recstart '>' --pipe blastp -query - -db /disks/jen/data/db/prokka_db/kingdom/Bacteria/sprot -evalue 1e-09 -qcov_hsp_perc 80 -num_threads 1 -num_descriptions 1 -num_alignments 1 -seg no > \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/sprot\.blast 2> /dev/null
[21:50:30] Modify product: Probable 2-dehydro-3-deoxygalactonokinase DgoK1 => putative 2-dehydro-3-deoxygalactonokinase DgoK1
[21:50:30] Modify product: Uncharacterized symporter YidK => putative symporter YidK
[21:50:30] Modify product: Putative aminotransferase MSMEG_6286/MSMEI_6121 => Putative aminotransferase/MSMEI_6121
[21:50:30] Modify product: UPF0701 protein YicC => hypothetical protein
[21:50:30] Modify product: Uncharacterized protein Rv1360 => putative protein
[21:50:30] Modify product: Probable sulfite/organosulfonate exporter TauE => putative sulfite/organosulfonate exporter TauE
[21:50:30] Modify product: Probable tRNA-dihydrouridine synthase => putative tRNA-dihydrouridine synthase
[21:50:30] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:30] Modify product: Uncharacterized protein Rv1367c => putative protein
[21:50:30] Modify product: Probable enoyl-CoA hydratase echA8 => putative enoyl-CoA hydratase echA8
[21:50:30] Modify product: Monocarboxylate 2-oxoacid-binding periplasmic protein all3028 => Monocarboxylate 2-oxoacid-binding periplasmic protein
[21:50:30] Modify product: Probable protease SohB => putative protease SohB
[21:50:30] Modify product: Probable peptidyl-prolyl cis-trans isomerase => putative peptidyl-prolyl cis-trans isomerase
[21:50:30] Modify product: Putative short-chain type dehydrogenase/reductase Rv0148 => Putative short-chain type dehydrogenase/reductase
[21:50:30] Modify product: Uncharacterized oxidoreductase Rv0769 => putative oxidoreductase
[21:50:30] Modify product: Uncharacterized protein Rv1360 => putative protein
[21:50:30] Modify product: Uncharacterized protein Rv1360 => putative protein
[21:50:30] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:30] Modify product: Uncharacterized FAD-linked oxidoreductase Rv2280 => putative FAD-linked oxidoreductase
[21:50:30] Modify product: Uncharacterized symporter YjmB => putative symporter YjmB
[21:50:30] Modify product: Probable enoyl-CoA hydratase => putative enoyl-CoA hydratase
[21:50:30] Modify product: Probable nicotinate-nucleotide adenylyltransferase => putative nicotinate-nucleotide adenylyltransferase
[21:50:30] Modify product: UPF0234 protein XC_3703 => hypothetical protein
[21:50:30] Modify product: UPF0716 protein FxsA => hypothetical protein
[21:50:30] Modify product: Uncharacterized protein YfgD => putative protein YfgD
[21:50:30] Modify product: Uncharacterized oxidoreductase YghA => putative oxidoreductase YghA
[21:50:30] Modify product: Uncharacterized protein Rv1367c => putative protein
[21:50:30] Modify product: Uncharacterized ABC transporter ATP-binding protein TM_0288 => putative ABC transporter ATP-binding protein
[21:50:30] Modify product: UPF0178 protein YaiI => hypothetical protein
[21:50:30] Modify product: Probable intracellular septation protein A => putative intracellular septation protein A
[21:50:30] Modify product: Uncharacterized protein YciO => putative protein YciO
[21:50:30] Modify product: Uncharacterized metal-dependent hydrolase YcfH => putative metal-dependent hydrolase YcfH
[21:50:30] Modify product: Uncharacterized ABC transporter ATP-binding protein Rv0986 => putative ABC transporter ATP-binding protein
[21:50:30] Modify product: Fanconi-associated nuclease 1 homolog => Fanconi-associated nuclease 1
[21:50:30] Modify product: UPF0758 protein YsxA => hypothetical protein
[21:50:30] Modify product: Uncharacterized protein Rv1367c => putative protein
[21:50:30] Modify product: Probable L-galactonate transporter => putative L-galactonate transporter
[21:50:30] Modify product: GTP-binding protein TypA/BipA homolog => GTP-binding protein TypA/BipA
[21:50:30] Modify product: Uncharacterized protein YibN => putative protein YibN
[21:50:30] Modify product: Probable Fe(2+)-trafficking protein => putative Fe(2+)-trafficking protein
[21:50:30] Modify product: Multifunctional alkaline phosphatase superfamily protein pRL90232 => Multifunctional alkaline phosphatase superfamily protein
[21:50:30] Modify product: Putative multidrug export ATP-binding/permease protein SA1683 => Putative multidrug export ATP-binding/permease protein
[21:50:30] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:30] Modify product: UPF0307 protein PSPTO_4464 => hypothetical protein
[21:50:30] Modify product: Probable xanthine dehydrogenase subunit A => putative xanthine dehydrogenase subunit A
[21:50:30] Modify product: Uncharacterized oxidoreductase SA2266 => putative oxidoreductase
[21:50:30] Modify product: Probable lipid II flippase MurJ => putative lipid II flippase MurJ
[21:50:30] Modify product: UPF0301 protein YqgE => hypothetical protein
[21:50:30] Modify product: Uncharacterized protein Rv3633 => putative protein
[21:50:30] Modify product: Probable sulfoacetate transporter SauU => putative sulfoacetate transporter SauU
[21:50:30] Modify product: UPF0246 protein YaaA => hypothetical protein
[21:50:30] Modify product: Probable enoyl-CoA hydratase echA8 => putative enoyl-CoA hydratase echA8
[21:50:30] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:30] Modify product: Uncharacterized protein Rv0953c => putative protein
[21:50:30] Modify product: Uncharacterized protein Rv1367c => putative protein
[21:50:30] Modify product: Uncharacterized protein Rv2913c => putative protein
[21:50:30] Modify product: Uncharacterized protein Rv3633 => putative protein
[21:50:30] Modify product: Probable polyketide biosynthesis zinc-dependent hydrolase BaeB => putative polyketide biosynthesis zinc-dependent hydrolase BaeB
[21:50:30] Modify product: Probable sulfate transporter Rv1739c => putative sulfate transporter
[21:50:30] Modify product: Probable glycine dehydrogenase (decarboxylating) subunit 2 => putative glycine dehydrogenase (decarboxylating) subunit 2
[21:50:30] Modify product: Probable FMN/FAD exporter YeeO => putative FMN/FAD exporter YeeO
[21:50:30] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:31] Modify product: Putative monooxygenase Rv1533 => Putative monooxygenase
[21:50:31] Modify product: Probable L-galactonate transporter => putative L-galactonate transporter
[21:50:31] Modify product: Uncharacterized amino acid permease YhdG => putative amino acid permease YhdG
[21:50:31] Modify product: Probable DNA endonuclease SmrA => putative DNA endonuclease SmrA
[21:50:31] Modify product: Uncharacterized oxidoreductase Rv1144 => putative oxidoreductase
[21:50:31] Modify product: Probable peptidoglycan glycosyltransferase FtsW => putative peptidoglycan glycosyltransferase FtsW
[21:50:31] Modify product: Uncharacterized protein Rv0953c => putative protein
[21:50:31] Modify product: Putative trans-acting enoyl reductase Rv2449c => Putative trans-acting enoyl reductase
[21:50:31] Modify product: Probable acyltransferase YihG => putative acyltransferase YihG
[21:50:31] Modify product: Probable enoyl-CoA hydratase echA8 => putative enoyl-CoA hydratase echA8
[21:50:31] Modify product: Uncharacterized oxidoreductase SA2266 => putative oxidoreductase
[21:50:31] Modify product: Uncharacterized protein Rv3633 => putative protein
[21:50:31] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:31] Modify product: Uncharacterized protein Rv1367c => putative protein
[21:50:31] Modify product: Uncharacterized protein YisK => putative protein YisK
[21:50:31] Modify product: Oxygen-independent coproporphyrinogen-III oxidase-like protein sll1917 => Oxygen-independent coproporphyrinogen-III oxidase-like protein
[21:50:31] Modify product: UPF0382 membrane protein YwdK => hypothetical protein
[21:50:31] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:31] Modify product: UPF0102 protein RPA0323 => hypothetical protein
[21:50:31] Modify product: Probable glycine dehydrogenase (decarboxylating) subunit 1 => putative glycine dehydrogenase (decarboxylating) subunit 1
[21:50:31] Modify product: Probable bifunctional transcriptional activator/DNA repair enzyme AlkA => putative bifunctional transcriptional activator/DNA repair enzyme AlkA
[21:50:31] Modify product: UPF0070 protein HI_0370 => hypothetical protein
[21:50:31] Modify product: Uncharacterized protein Rv2000 => putative protein
[21:50:31] Modify product: Probable enoyl-CoA hydratase echA8 => putative enoyl-CoA hydratase echA8
[21:50:31] Modify product: Uncharacterized protein Rv0953c => putative protein
[21:50:31] Modify product: Putative acyltransferase Rv0859 => Putative acyltransferase
[21:50:31] Modify product: Putative short-chain type dehydrogenase/reductase Rv0148 => Putative short-chain type dehydrogenase/reductase
[21:50:31] Modify product: Putative coenzyme F420-dependent oxidoreductase Rv3520c => Putative coenzyme F420-dependent oxidoreductase
[21:50:31] Modify product: UPF0324 inner membrane protein YeiH => hypothetical protein
[21:50:31] Modify product: UPF0053 inner membrane protein YfjD => hypothetical protein
[21:50:31] Modify product: Uncharacterized protein Rv1501 => putative protein
[21:50:31] Modify product: Uncharacterized ABC transporter ATP-binding protein TM_0288 => putative ABC transporter ATP-binding protein
[21:50:31] Modify product: F420H(2)-dependent quinone reductase Rv1558 => F420H(2)-dependent quinone reductase
[21:50:31] Modify product: Uncharacterized protein YbbN => putative protein YbbN
[21:50:31] Modify product: Uncharacterized ABC transporter ATP-binding protein YheS => putative ABC transporter ATP-binding protein YheS
[21:50:31] Modify product: Uncharacterized protein Rv2913c => putative protein
[21:50:31] Modify product: Uncharacterized protein Rv2913c => putative protein
[21:50:31] Modify product: Probable GTP-binding protein EngB => putative GTP-binding protein EngB
[21:50:31] Modify product: Probable enoyl-CoA hydratase 1 => putative enoyl-CoA hydratase 1
[21:50:31] Modify product: UPF0434 protein NMA0874 => hypothetical protein
[21:50:31] Modify product: Uncharacterized hydrolase YxeP => putative hydrolase YxeP
[21:50:31] Cleaned 103 /product names
[21:50:31] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/sprot.faa
[21:50:31] Deleting unwanted file: /disks/jen/data/mres/ndebono/bin.1.prokka.out/sprot.blast
[21:50:31] There are still 876 unannotated CDS left (started with 1884)
[21:50:31] Will use hmmer3 to search against /disks/jen/data/db/prokka_db/hmm/HAMAP.hmm with 8 CPUs
[21:50:31] Running: cat \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/HAMAP\.hmm\.faa | parallel --gnu --plain -j 8 --block 13716 --recstart '>' --pipe hmmscan --noali --notextw --acc -E 1e-09 --cpu 1 /disks/jen/data/db/prokka_db/hmm/HAMAP.hmm /dev/stdin > \/disks\/jen\/data\/mres\/ndebono\/bin\.1\.prokka\.out\/HAMAP\.hmm\.hmmer3 2> /dev/null~~~
~~~
{: .output}


PROKKA will give us a lot of output files that we will find useful

~~~
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.log
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.fna
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.gff
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.faa
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.ffn
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.tbl
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.fsa
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.tsv
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.txt
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.sqn
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.err
[21:50:53] /disks/jen/data/mres/ndebono/bin.1.prokka.out/PROKKA_10202018.gbk

~~~
{: .output}



> ## Exercise
>
> Have a look at the contents of each file. What information does each contain?
> following questions.
>
> 1) How many tRNA genes were predicted in bin.1?
> 2) How genes were predicted in bin.1?
> 3) Is bin.1 likely from a photosynthetic porganism?
> 4) Name some of the functional genes predicted in bin.1
> 4) Which database did PROKKA use to assign those functional annotations?
>> ## Solution
>>
>>
> {: .solution}
{: .challenge}

But how many bins do you have to annotate?


**Loops** are key to productivity improvements through automation as they allow us to execute
commands repetitively. Similar to wildcards and tab completion, using loops also reduces the
amount of typing (and typing mistakes).


Instead, we can use a **loop**
to do some operation once for each thing in a list.
Here's a simple example that displays the first three lines of each file in turn:

~~~
$ for i in *fa
> do
>    name=$(basename $i .fa)	# Indentation within the loop aids legibility
>    /usr/local/anaconda2/bin/prokka $i -o $name.prokka.out
> done
~~~
{: .language-bash}

When the shell sees the keyword `for`,
it knows to repeat a command (or group of commands) once for each item in a list.
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to
the **variable**, and the commands inside the loop are executed, before moving on to
the next item in the list.
Inside the loop,
we call for the variable's value by putting `$` in front of it.
The `$` tells the shell interpreter to treat
the **variable** as a variable name and substitute its value in its place,
rather than treat it as text or an external command.



## Taxonomic Assignment

-moved to week 4.


## Super-Focus:

 A tool for agile functional analysis of metagenomic data

~~~
Silva, G. G. Z., Green K., B. E. Dutilh, and R. A. Edwards:
SUPER-FOCUS: A tool for agile functional analysis of shotgun metagenomic data.
Bioinformatics. 2015 Oct 9. pii: btv584. Website: https://edwards.sdsu.edu/SUPERFOCUS
~~~


Installation

~~~
/usr/local/anaconda2/bin/conda create -n py36 python=3.6 anaconda

/usr/local/anaconda2/bin/conda activate py36

Pip install --user superfocus --force
~~~
{: .language-bash}


SUPER-FOCUS is fussy about the name of the input files. To fix this problem we’ll make a copy of the contains in a new directory

Make a copy of your bins.fa
~~~
mkdir bins.copy

cp *fa bins.copy/
~~~
{: .language-bash}

Then replace the `.fa` with `.fasta` file extension using this script
~~~
cd bins.copy

rename ’s/.fa/.fasta/‘ *
~~~
{: .language-bash}


Then we are ready to run SUPER-FOCUS

~~~
superfocus -a diamond -q ./ -n 1 -t 8 -dir test -b /disks/jen/scratch-ssd/db/superfocus_db/

~~~
{: .language-bash}

