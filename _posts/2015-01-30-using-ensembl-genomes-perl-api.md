---
layout: post
title: "Using Ensembl Genomes Perl API"
categories:
- Bioinfo tools
tags:
- Ensembl Perl API
- bacteria

comments: true
---
Using EnsEMBL Genomes Perl API

URL: [http://ensemblgenomes.org/info/access/eg_api]() 

Up to date: 2015-01-30, using Ensembl version v78.

### Notes

Before you read the following instructions, you are supposed to know about the `Ensembl Perl API`.  The follow 2 links are very helpful.

* Ensembl Perl API tutorials: [here](http://asia.ensembl.org/info/docs/api/index.html)
* Ensembl, Filmed API Workshop: [here](http://www.ebi.ac.uk/training/online/course/ensembl-filmed-api-workshop)  

### Begin

Things to know about Ensembl Genomes: (different from Ensembl Core)

* 1. Load more than 250 genomes in a single database for bacteria.

> Whilst all Ensembl Genomes database can be accessed using the standard Ensembl API, the way that up to `250 genomes are loaded into a single database` presents some barriers to easy access for Ensembl Bacteria. To overcome this, a simple API, ensemblgenomes-api is provided to make accessing the data easier.  
--Ensembl genomes

* 2. Using different central object: `Bio::EnsEMBL::LookUp`

> This provides an interface for loading the EnsEMBL Registry with the large numbers of genomes from multiple databases, and allows individual DBAdaptor objects to be retrieved for genomes that match criteria including ENA accession (or other seq_region name), Genome Assembly accession, species name and taxonomy ID.  
Once Bio::EnsEMBL::DBAdaptor objects have been returned, they can be used as for any Ensembl species. Alternatively, the now-loaded Bio::EnsEMBL::Registry object can be accessed directly.  
--Ensembl genomes


### Installing the API  
> First, the standard Ensembl API and its dependencies should be installed as described [here](http://www.ensembl.org/info/docs/api/api_installation.html). This should be the same version of Ensembl that is used by Ensembl Bacteria. 

> Secondly, the `ensemblgenomes-api` API should be installed from the [GitHub repository](http://github.com/EnsemblGenomes/ensemblgenomes-api). You will need to add the modules directory from this package to your PERL5LIB. You may also need to install the CPAN package JSON - please consult your local systems adminstration if you are unsure about how to do this.  
--Ensembl genomes

### Basic usage

> The default mode for using the API is to use a specialised lookup database on the public Ensembl Genomes server. The usage of this is shown below, but note that there is a pregenerated snippet of code for each DBAdaptor on the species description page in the genome browser. Note that full PerlDoc is available from the API or [here](http://ensemblgenomes.github.io/ensemblgenomes-api/annotated.html)
--Ensembl genomes

Example 1: Retrieve gene/... informations

```{perl}
use strict;
use warnings;

use Bio::EnsEMBL::LookUp;
my $lookup = Bio::EnsEMBL::LookUp->new();

# Getting DBAdaptors
# 1. by name
$dba = $lookup->get_by_name_exact('escherichia_coli_str_k_12_substr_mg1655');
$dba = $lookup->get_all_by_name_pattern('escherichia_coli_str_k_12_*');

# 2. by taxonomy
$dba = $lookup->get_all_by_taxon_id(388919); # 
$dba = $lookup->get_all_by_taxon_branch(561); # branch id

# 3. by genomic INSDC accession
$dba = $lookup->get_all_by_accession('U00096');

# 4. by genome assembly accession
$dba = $lookup->get_by_assembly_accession('GCA_000005845.2') # E. coli

```

supportting material:   

```
# E. coli K-12 MG1655
Organism name: Escherichia coli str. K-12 substr. MG1655
Infraspecific name: Strain: K-12 substr. MG1655
BioSample: SAMN02604091
Submitter: Univ. Wisconsin
Date: 2013/09/26
Assembly level: Complete Genome
Genome representation: full
RefSeq category: reference genome
GenBank assembly accession: GCA_000005845.2 (latest)
RefSeq assembly accession: GCF_000005845.2 (latest)
RefSeq assembly and GenBank assembly identical: yes
IDs: 79781 [UID] 854978 [GenBank] 854998 [RefSeq]

Molecule name    GenBank sequence        RefSeq sequence
Chromosome    U00096.3    =    NC_000913.3
```

### Advanced usages:

The ensemblgenomes-api API can be used in conjunction with the Compara Perl API to access gene family data for Bacteria. We can also use Registry to get the family data.
(see the script 1)

    * 1. Retrieve which family a gene belonging to?  
    * 2. Retrieve genes belonging to a family.  
    * 3. Retrieve genes belonging to a family, within a specific specie.  
    * 4. Retrieve canonical peptides from a family.    
  
  

* 1. Retrieve which famil a genen belonging to? (Two ways to get geneAdaptors of specific gene.)

see [script on github](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/eg_ex2_1_retrieve_family_of_genes.pl) and the [output](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/ex2_1.out).

```{perl}
use strict;
use warnings;

use Bio::EnsEMBL::Registry;
use Bio::EnsEMBL::LookUp;
use Bio::EnsEMBL::Compara::DBSQL::DBAdaptor;

use Data::Dumper;

# Retrieve the family of a given gene

#my $nom = 'mycobacterium_tuberculosis_h37rv_gca_000195955_2';
#my $input_gene = 'Rv2925c'; # RNase III
my $nom = 'escherichia_coli_str_k_12_substr_mg1655';
my $input_gene = 'b0344';

my $lookup = Bio::EnsEMBL::LookUp->new();

# load gene adaptor
my $dba = $lookup->get_by_name_exact($nom);

my $gene_adaptor = $dba->get_GeneAdaptor();

my $gene = $gene_adaptor->fetch_by_stable_id($input_gene); # lacZ

# OPTIONS 1, Bio::EnsEMBL::Registry
# load Registry
my $registry = 'Bio::EnsEMBL::Registry';
$registry->load_registry_from_db(
        -host => 'mysql-eg-publicsql.ebi.ac.uk',
        -port => '4157',
        -user => 'anonymous'
        );

my $member_adaptor = $registry->get_adaptor('bacteria', 'compara', 'GeneMember');

my $family_adaptor = $registry->get_adaptor('bacteria', 'compara', 'Family');

my $member = $member_adaptor->fetch_by_stable_id($input_gene);

my $families = $family_adaptor->fetch_all_by_GeneMember($member);

# OPTIONS 2, Compara
## load compara adaptor
#my $compara_dba = Bio::EnsEMBL::Compara::DBSQL::DBAdaptor->new(
#        -host => 'mysql-eg-publicsql.ebi.ac.uk',
#        -port => '4157',
#        -user => 'anonymous',
#        -dbname => 'ensembl_compara_bacteria_25_78'
#        );
#
#my $member = $compara_dba->get_GeneMemberAdaptor()->fetch_by_stable_id($input_gene);

#my $families = $compara_dba->get_FamilyAdaptor()->fetch_all_by_GeneMember($member);

# loop out
print "Retrieve the family of input gene: ", $input_gene, "\n", "From genome: ", $nom, "\n\n";
print "Family", "\t", "Family_description", "\n";

foreach my $family (@{$families}){
    # this family
    print $family->stable_id(), "\t", $family->description(), "\n";
}

```

* 2. Retrieve genes belonging to a family

see [script on github](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/eg_ex2_2_retrieve_genes_belonging_to_a_family.pl) and the [output](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/ex2_2.out).

```{perl}
use strict;
use warnings;

#use Bio::EnsEMBL::Registry;
use Bio::EnsEMBL::LookUp;
use Bio::EnsEMBL::Compara::DBSQL::DBAdaptor;

use Data::Dumper;

my $input_family = 'MF_00395'; #

# Retrieve genes from a given family
my $lookup = Bio::EnsEMBL::LookUp->new();

# load compara adaptor
my $compara_dba = Bio::EnsEMBL::Compara::DBSQL::DBAdaptor->new(
        -host => 'mysql-eg-publicsql.ebi.ac.uk',
        -port => '4157',
        -user => 'anonymous',
        -dbname => 'ensembl_compara_bacteria_25_78'
        );

my $family = $compara_dba->get_FamilyAdaptor()->fetch_by_stable_id($input_family);

# output
print "Retrieve genes from a given family\n";
print "Input family: ", $family->stable_id(), " ", $family->description(), "\n\n";

print "Output:\n", "Gene", "\t", "GeneName", "\t", "Species", "\n";
# find members in the family
my $members = $family->get_all_Members();
foreach my $member (@{$members}){
    my $genome_db = $member->genome_db();

    # member dba
    my $member_dba = $lookup->get_by_name_exact($genome_db->name());
    if(defined $member_dba){
        my $gene_adaptor = $member_dba->get_GeneAdaptor();
        my $gene = $gene_adaptor->fetch_by_stable_id($member->gene_member()->stable_id());
        my $gene_external_name  = (defined $gene->external_name())?$gene->external_name():"-";
        print $gene->stable_id(), "\t", $gene_external_name, "\t", $member_dba->species(), "\n";
        $member_dba->dbc()->disconnect_if_idle();
    }
}

```

* 3. Retrieve genes belonging to a family, within a specific specie

see [script on github](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/eg_ex2_3_retrieve_genes_belonging_to_a_family_of_species.pl) and the [output](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/ex2_3.out).

```{perl}
use strict;
use warnings;

#use Bio::EnsEMBL::Registry;
use Bio::EnsEMBL::LookUp;
use Bio::EnsEMBL::Compara::DBSQL::DBAdaptor;

use Data::Dumper;

my $input_family = 'MF_00395';
my $taxid = 1219; #Prochlorococcus marinus

# find all genomes that descendants of a specified node (as a filter)
my $lookup = Bio::EnsEMBL::LookUp->new();

my $branches = $lookup->get_all_by_taxon_branch($taxid);
my %target_species = map {$_->species() => $_ } @{$branches};

# load compara adaptors
my $compara_dba = Bio::EnsEMBL::Compara::DBSQL::DBAdaptor->new(
        -host => 'mysql-eg-publicsql.ebi.ac.uk',
        -port => '4157',
        -user => 'anonymous',
        -dbname => 'ensembl_compara_bacteria_25_78'        
        );
my $family = $compara_dba->get_FamilyAdaptor()->fetch_by_stable_id($input_family);

print "Retrieve genes from a given family, of specific genomes\n";
print "Input family: ", $family->stable_id(), "\t", $family->description(), "\n";

# find members in this family
print "Find members of taxid 1219 in this family\n\n";
print "GeneID", "\t", "Species", "\n";
my $members = $family->get_all_Members();
foreach my $member (@{$members}){
    my $genome_db = $member->genome_db();
     
    # filter by taxon from the list
    my $member_dba = $target_species{$genome_db->name()};
    if(defined $member_dba){
        my $gene_adaptor = $member_dba->get_GeneAdaptor();
        my $gene = $gene_adaptor->fetch_by_stable_id($member->gene_member()->stable_id());
        my $gene_external_name  = (defined $gene->external_name())?$gene->external_name():"-";
        print $gene->stable_id(), "\t", $gene_external_name, "\t", $member_dba->species(), "\n";
        $member_dba->dbc()->disconnect_if_idle();
    }
}

```

* 4. Retrieve canonical peptides from a family

see [script on github](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/eg_ex2_4_retrieve_canonical_peptides_from_genes_of_a_family.pl), and the [output](https://raw.githubusercontent.com/bakerwm/RNASeq-dataanalysis/master/ensembl_genomes/ex2_4.out).

```{perl}
use strict;
use warnings;

#use Bio::EnsEMBL::Registry;
use Bio::EnsEMBL::LookUp;
use Bio::SeqIO;

use Data::Dumper;

# Retrieve the canonical peptides from genes belonging to a given family

# Input data
my $input_family = 'MF_00395';

# lookup
my $lookup = Bio::EnsEMBL::LookUp->new();

# load compara 
my $compara_dba = Bio::EnsEMBL::Compara::DBSQL::DBAdaptor->new(
        -host => 'mysql-eg-publicsql.ebi.ac.uk',
        -port => '4157',
        -user => 'anonymous',
        -dbname => 'ensembl_compara_bacteria_25_78'
        );

my $family = $compara_dba->get_FamilyAdaptor()->fetch_by_stable_id($input_family);

my $outfile = ">". $family->stable_id. ".fa";
my $seq_out = Bio::SeqIO->new(-file => $outfile,
                              -format => "fasta"
                              );

print "Write family: ", $family->stable_id(), " to $outfile\n";

# loop over members
my $members = $family->get_all_Members();
foreach my $member (@{$members}){
    my $genome_db = $member->genome_db();
    my $member_dba = $lookup->get_by_name_exact($genome_db->name());
    if(defined $member_dba){
        my $gene = $member_dba->get_GeneAdaptor()->fetch_by_stable_id($member->gene_member()->stable_id() );
        print "Write sequence of ", $member->stable_id(), "\n";
        my $s = $gene->canonical_transcript()->translate();
        $seq_out->write_seq($s);
        $member_dba->dbc()->disconnect_if_idle();
    }

}
```



