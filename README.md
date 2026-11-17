# Treeclimber: An Ontology Alignment Analysis Tool

Treeclimber analyzes the alignment of groups of ontology files with an upper-level ontology, reports on alignment on a class-by-class and ontology-by-ontology basis, and identifies unaligned branches of terms. It was created for the OBO Foundry to analyze the alignment of active OBO ontologies with the Core Ontology for Biology and Biomedicine.

## Using Treeclimber

Treeclimber reads through RDF/XML files in the `cache/` directory, examining each class for whether it has an ancestor that is a term in COB, and if not, what its highest in-namespace ancestor is.

Using it requires installing [Rust](https://doc.rust-lang.org/book/ch01-01-installation.html).

### Optional: Downloading OBO Ontologies

With Rust installed, run the following command from within the repository:

```
cargo run --release -- download
```

Appending `-l` or `--lazy` makes Treeclimber skip downloading ontologies that already exist in the `cache/` directory (or in a directory called `unparseable/` further described in the next section). If you get part of the way through downloading ontologies and must restart the script, use `-l` to avoid redownloading what you've already got.

Appending `-t <n>` or `--test-length <n>` makes Treeclimber stop attempting to download files after doing so for _n_ files.

Alternatively, place any number of ontology files into the `cache/` directory.

### Running Alignment Analysis on Cached Ontologies

After adding ontologies to the `cache/` directory, run the following command from within the repository to perform alignment analysis:

```
cargo run --release -- analyze <args>
```

The arguments `--ulo-path path/to/ulo.owl` (`-p`) and `--ulo-iri https://example.com/ulo.owl` (`i`) are used to set the upper-level ontology against which the cached ontologies will be compared. For instance, you can use the following command to analyze cached ontologies' alignment with the Basic Formal Ontology:

```
cargo run --release -- analyze -i http://purl.obolibrary.org/obo/bfo.owl
  
```

By default the analysis will only include in-namespace roots in the detailed unaligned analysis. However, the argument `--all-unaligned` (`-a`) will force the script to include all unaligned roots (incl. out-of-namespace roots) and show them in the `unaligned_roots` results file. 

#### RDFXML Parsing Errors

While running the analysis portion of the script, Treeclimber may abort with an RDFXML parsing error. When this occurs, move the file mentioned in the last `Analyzing classes in <ont>` printout from the `cache/` directory into the `unparseable/` directory, and then rerun the script.

### Results Files

The results of the script end up in [`results/`](results/).

[`results/alignment_analysis.tsv`](results/alignment_analysis.tsv) is a table of, for each ontology analyzed, the number of classes in and out of that ontology's namespace and in and out of alignment with the ULO (i.e., having or not having an ancestor that is a class in the ULO).

[`results/all_classes.tsv`](results/all_classes.tsv) is a table of every class in every ontology the script analyzed, including for each class its IRI & label, the IRI & label of its lowest in-ULO ancestor if one exists, the ontology in which that class was found, whether it is in that ontology's namespace, and if so and if it has no in-ULO ancestor, the IRI & label of its highest in-namespace ancestor.

[`results/unaligned_roots.tsv`](results/unaligned_roots.tsv) is a table of IRIs and labels of unaligned roots (i.e., highest in-namespace classes without in-ULO ancestors), the ontology they are from, whether they are marked as a preferred root (via an `IAO:0000700` annotation), and how many descendent terms are under that root.
