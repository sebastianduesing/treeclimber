# Treeclimber: An Ontology Alignment Analysis Tool

Treeclimber analyzes the alignment of groups of ontology files with an upper-level ontology, reports on alignment on a class-by-class and ontology-by-ontology basis, and identifies unaligned branches of terms. It was created for the OBO Foundry and is presently configured to analyze the alignment of active OBO ontologies with the Core Ontology for Biology and Biomedicine.

## Using Treeclimber

Treeclimber downloads all active OBO ontologies and reads through the files, examining each class for whether it has an ancestor that is a term in COB, and if not, what its highest in-namespace ancestor is.

Using it requires installing [Rust](https://doc.rust-lang.org/book/ch01-01-installation.html).

### Downloading OBO Ontologies

With Rust installed, run the following command from within the repository:

```
cargo run --release -- download
```

Appending `-l` or `--lazy` makes Treeclimber skip downloading ontologies that already exist in the `cache/` directory (or in a directory called `unparseable/` further described in the next section). If you get part of the way through downloading ontologies and must restart the script, use `-l` to avoid redownloading what you've already got.

Appending `-t <n>` or `--test-length <n>` makes Treeclimber stop attempting to download files after doing so for _n_ files.

On my machine, downloading all active OBO ontologies takes about 5 minutes.

### Running COB Alignment Analysis on Downloaded Ontologies

After downloading ontologies, run the following command from within the repository:

```
cargo run --release -- analyze
```

On my machine, running the alignment analysis over all OBO ontologies takes about 3 minutes.

#### RDFXML Parsing Errors

While running the analysis portion of the script, Treeclimber may abort with an RDFXML parsing error. When this occurs, move the file mentioned in the last `Analyzing classes in <ont>` printout from the `cache/` directory into the `unparseable/` directory, and then rerun the script.

As of August 2026, when I run this script, the files I find that I need to move into `unparseable/` are `cto.owl`, `gaz.owl`, `ogg.owl`, and `sbo.owl`.

### Results Files

The results of the script end up in [`results/`](results/).

[`results/alignment_analysis.tsv`](results/alignment_analysis.tsv) is a table of, for each ontology analyzed, the number of classes in and out of that ontology's namespace and in and out of alignment with COB (i.e., having or not having a COB ancestor).

[`results/download_summary.tsv`](results/download_summary.tsv) is a table of the ontologies the script found in the OBO registry, their activity status, and whether it downloaded, skipped, or did not attempt to download that ontology.

[`results/obo_classes.tsv`](results/obo_classes.tsv) is a table of every class in every OBO Foundry ontology the script downloaded and analyzed, including for each class its IRI & label, the IRI & label of its lowest COB ancestor if one exists, the ontology in which that class was found, whether it is in that ontology's namespace, and if so and if it has no COB ancestor, the IRI & label of its highest in-namespace ancestor.

[`results/unaligned_roots.tsv`](results/unaligned_roots.tsv) is a table of IRIs and labels of unaligned roots (i.e., highest in-namespace classes without COB ancestors), the ontology they are from, whether they are marked as a preferred root (via an `IAO:0000700` annotation), and how many descendent terms are under that root.
