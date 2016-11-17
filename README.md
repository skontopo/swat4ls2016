Studying the Cohesion Evolution of Genes Related to Chronic Lymphocytic Leukemia Using Semantic Similarity in Gene Ontology and Self-Organizing Maps
===

This repository contains the data and code associated with the following paper:

E. Kontopoulos, T. Moysiadis, M. Tsagiopoulou, S. Darányi, P. Wittek, N. Papakonstantinou, S. Ntoufa, G. Meditskos, K. Stamatopoulos, I. Kompatsiaris. (2016). [Studying the Cohesion Evolution of Genes Related to Chronic Lymphocytic Leukemia Using Semantic Similarity in Gene Ontology and Self-Organizing Maps](https://www.researchgate.net/profile/Efstratios_Kontopoulos/publication/310254239_Studying_the_Cohesion_Evolution_of_Genes_Related_to_Chronic_Lymphocytic_Leukemia_Using_Semantic_Similarity_in_Gene_Ontology_and_Self-Organizing_Maps/links/582ad3a008ae138f1bf40300.pdf). Proceedings of [SWAT4LS-16](http://www.swat4ls.org/workshops/amsterdam2016/), 9th Semantic Web Applications and Tools for Life Sciences Workshop, December 2016.

The outline of the processing steps is as follows:

1. Index the subsequent time periods by Lucene.
2. Build random indices.
3. Train emergent self-organizing maps.

The rest of this readme details these steps. The dependencies for the Java tools are lucene-core-4.10.3.jar, lucene-analyzers-common-4.10.3.jar, and edu.mit.jwi_2.3.3.jar, and [SemanticVectors](https://github.com/semanticvectors/semanticvectors) 5.8.

Indexing
--------
Run the class ``concepDrifts.PubMedAbstractIndexer`` with setting the parameters in the ``main()`` function for each period 1 to 3. This will yield three index directories.

Generating the random indices
---------------------------------------
We build the random indices in the data folder.

    java -Xmx40000m pitt.search.semanticvectors.BuildIndex -luceneindexpath index1
    mv termvectors2.bin termvectorsperiod-1.bin
    mv docvectors2.bin docvectorsperiod-1.bin

    java -Xmx40000m pitt.search.semanticvectors.BuildIndex -luceneindexpath index2
    mv termvectors2.bin termvectorsperiod-2.bin
    mv docvectors2.bin docvectorsperiod-2.bin

    java -Xmx40000m pitt.search.semanticvectors.BuildIndex -luceneindexpath index3
    mv termvectors2.bin termvectorsperiod-3.bin
    mv docvectors2.bin docvectorsperiod-3.bin

We need to convert the term vectors to text format:

    java pitt.search.semanticvectors.VectorStoreTranslater -lucenetotext termvectorsperiod-1.bin termvectorsperiod-1.txt
    java pitt.search.semanticvectors.VectorStoreTranslater -lucenetotext termvectorsperiod-2.bin termvectorsperiod-2.txt
    java pitt.search.semanticvectors.VectorStoreTranslater -lucenetotext termvectorsperiod-3.bin termvectorsperiod-3.txt

Then we transform the random index to suitable input files for Somoclu and ESOM Tools:

    java conceptDrifts.SvDense2Sparse termvectorsperiod-1.txt termvectorsperiod-1.svm termvectorsperiod-1.names
    java conceptDrifts.SvDense2Sparse termvectorsperiod-2.txt termvectorsperiod-2.svm termvectorsperiod-2.names
    java conceptDrifts.SvDense2Sparse termvectorsperiod-3.txt termvectorsperiod-3.svm termvectorsperiod-3.names

Training the emergent self-organizing maps
------------------------------------------
We used [Somoclu](https://peterwittek.github.io/somoclu/) to train emergent self-organizing maps on a toroid topology:

```bash
somoclu -k 2 -m toroid -s 1 -x 176 -y 100 termvectorsperiod-1.svm termvectorsperiod-1
somoclu -k 2 -m toroid -c data/termvectorsperiod-1.wts -s 1 -x 176 -y 100 termvectorsperiod-2.svm termvectorsperiod-2
somoclu -k 2 -m toroid -c data/termvectorsperiod-2.wts -s 1 -x 176 -y 100 termvectorsperiod-3.svm termvectorsperiod-3
```

Acknowledgment
===
This work was supported by the European Commission Seventh Framework Programme under Grant Agreement Number FP7-601138 PERICLES.
