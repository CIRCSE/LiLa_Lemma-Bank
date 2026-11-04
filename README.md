# LiLa_Lemma-Bank
The LiLa Lemma Bank is the core of the LiLa Knowledge Base. It consists of a large collection of
Latin lemmas, serving as the backbone to achieve interoperability between the resources, by linking all those entries in lexical resources and tokens in corpora that point to the same lemma.

Data are coded both in a relational database ( SQL format ) and in a graph database (RDF triples - Turtle format).
Mapping between SQL and RDF data is made by means of D2RQ according to the mapping file provided in the 'rdf' folder.

##  RELATIONAL DATABASE DESCRIPTION

[MariaDB](https://mariadb.com/) gzipped dump is provided in the root folder.
Row counts given below are those of commit `1a1ee92` (dump completed 2026-06-09 14:22:24); they change with each dump.
 
### Main tables

* **lemmas** 
```
lemma
```
Each field refers to the corresponding metadata table:
```
 flectional_category 
 gender              
 grade               
 plurality           
 universal_pos_tag   
```


* **hypolemmas**
```
 ipolemma            
```
Field `type` refers to the table
```
ipolemmaType
```
Where each hypolemma type is related to an attribute set.


* Written Representations
```
 lemma_wr            
 ipolemma_wr         
```


* (hypo)lemma - hypolemma relations
```
lemma_ipolemma
ipolemma_ipolemma
```

* Lemma variants 
```
variant_group
``` 

* Phonetic Representations
```
phonetic_rep
```

* Word Formation:
    - `base`
    - `lemma_base` 
    - `lemma_suffix`
    - `lemma_prefix`
    
    Referred **Suffixes**  and  **Prefixes** are coded in tables:
    - `suffix`
    - `prefix`
    
    Some hypolemmas are explicitly related to suffixes too:
    - `ipolemma_suffix`



### RDF mapping exception
All hypolemmas are mapped to RDF except the ones of type Comparative and Superlative.
Comparatives (and superlatives) referred in table ```hypolemmaCompSup``` are exceptions and are thus mapped.

Note: the published `rdf/lemmaBank.ttl` contains all comparative and superlative hypolemmas
(16,647 `COMP` and 16,677 `SUP`), while the conditions in `rdf/lemmaBankMap.ttl` (lines 149 and 274)
keep only the 58 `COMP`/`SUP` hypolemmas listed in `hypolemmaCompSup` (39 `COMP`, 19 `SUP`; the table also lists
7 `COMP_ADV` and 2 `SUP_ADV`, which the conditions do not filter).
The two files therefore differ on this point.



### LemLat - LiLa Relations

Some lemmas or hypolemmas are related to one or more LemLat lemma(s):
```
 lemlat_lila        
``` 



### Main look-up a.k.a. table

```
lilaLU
``` 
Materialized View putting together lemmas and hypolemmas.
The three fields ```id_lemma,id_ipolemma0,id_ipolemma1``` show the 'path' of 
each lemma or hypolemma according to the  ```class```: 
- `0` for  lemma, 
- `1` for  hypolemma of lemma, 
- `2` for  hypolemma of hypolemma

All written representations for each entry are listed in the field ```wrList```

Note: `lilaLU` lists paths, not entries. A hypolemma has one row per parent, so its
64,385 hypolemma rows cover 62,575 of the 95,844 hypolemmas. The other 33,269 hypolemmas have no row;
33,262 of them are `COMP`/`SUP` hypolemmas not listed in `hypolemmaCompSup`, in line with the exception above.
For a complete inventory, use `lemma` and `ipolemma`.


### Deprecated identifiers

```
deprecatedLemma
deprecatedHypolemma
deprecatedHypoHypolemma
```
Withdrawn lemmas (513 rows, 511 IDs: `lemma/139376` has one row per successor and `lemma/56713` is listed twice),
and withdrawn hypolemmas listed with the lemma (475) or hypolemma (3) they belonged to.
None of the 511 lemma IDs is in `lemma`; 4 of the hypolemma IDs in `deprecatedHypolemma` are still in `ipolemma`.
In `deprecatedLemma` and `deprecatedHypolemma`, the fields `byLemma` and `byHypolemma` give the successor,
when there is one; a successor may sit in the other namespace (a deprecated lemma replaced by a hypolemma, or vice versa).
These tables exist only in the SQL dump: the RDF publishes no deprecation, so a link from another resource
to a withdrawn lemma ID, or to a withdrawn hypolemma ID no longer in `ipolemma`, points to no node.
In five lexical resources linked to LiLa (WFL, Lewis & Short, Lexicon Bohemorum, Lexicala, Neulateinische Wortliste),
every one of the 46 `ontolex:canonicalForm` links that point to no node targets a deprecated ID.


### Other tables

Observed in the dump, not described above:
- `superlemma` (264,640): the `id_super` keys on which `variant_group` groups lemmas and hypolemmas
- `lemma_legacy` (131,252): the columns of `lemma` except `id_super`, with `id_lemma` a text code of six `_`-separated parts beginning `n_id`, POS, `fl_cat`, `gen` (e.g. `46261_PROPN_n2n_n_1_s`); the fifth part follows `grad`
- `unlinkedHypo` (593): hypolemma IDs not in `ipolemma` (POS_ADV, COMP, SUP and 10 participles); 3 of them are in a deprecation table
- `new_ipo` (29): `id_lemma, wr, upostag, id_ipolemma`; 28 rows are live POS_ADV hypolemmas
- `omografi` (4): adverbs such as *parabolice*, each with a live POS_ADV hypolemma and an ADV lemma ID that is in neither `lemma` nor `deprecatedLemma`
- `lila_it` (24): `id_lemma, lemmaIT`, a label with a `^` qualifier (`occido^caedo`, `occido^cado`, `os^oris`, `os^ossis`); 22 of the 24 lemmas share their label with another lemma
- `source` (3): the codes `B`, `D`, `O`


### Views
Some support views are provided.



## RDF mapping

The Relational Database is mapped to the Graph Database by means of the [D2RQ platform](http://d2rq.org/).
The mapping file is provided in the ```rdf``` folder.
Triples are produced with the command:

```
./dump-rdf -f TURTLE -o lemmaBank lemmaBankMap.ttl
```

after editing the mapping file specifying the database access parameters.


## Credits

- **Creators**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Federica Iurescia, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Giulia Pedonese, Matteo Pellegrini, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori
- **Contributors**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Federica Iurescia, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Giulia Pedonese, Matteo Pellegrini, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori

The _LiLa: Linking Latin_ project has received funding from the European Research Council (ERC) under the European Union's Horizon 2020 research and innovation programme – Grant Agreement No. 769994.

## Copyright
<a rel="license" href="https://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />These resources are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

## Citation
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4017229.svg)](https://doi.org/10.5281/zenodo.4017229)

[![DOI](https://zenodo.org/badge/296268550.svg)](https://zenodo.org/badge/latestdoi/296268550)

The first badge is the paper describing the Lemma Bank:

Passarotti, M., Mambrini, F., Franzini, G., Cecchini, F. M., Litta, E., Moretti, G., Ruffolo, P. & Sprugnoli, R. (2020).
Interlinking through Lemmas. The Lexical Collection of the LiLa Knowledge Base of Linguistic Resources for Latin.
*Studi e Saggi Linguistici* 58(1): 177–212. https://doi.org/10.4454/ssl.v58i1.277 (Zenodo copy: https://doi.org/10.5281/zenodo.4017229)

The second badge is the archive of this repository on Zenodo: https://doi.org/10.5281/zenodo.4289883 resolves to
the latest release. When citing the data, please also give the release or commit you used.

The Lemma Bank in Turtle format is also available in the ILC4CLARIN repository of CLARIN-IT: [http://hdl.handle.net/20.500.11752/OPEN-532](http://hdl.handle.net/20.500.11752/OPEN-532).

