# LiLa_Lemma-Bank
The LiLa Lemma Bank is the core of the LiLa Knowledge Base. It consists of a large collection of
Latin lemmas, serving as the backbone to achieve interoperability between the resources, by linking all those entries in lexical resources and tokens in corpora that point to the same lemma.

Data are coded both in a relational database ( SQL format ) and in a graph database (RDF triples - Turtle format).
Mapping between SQL and RDF data is made by means of D2RQ according to the mapping file provided in the 'rdf' folder.

##  RELATIONAL DATABASE DESCRIPTION

[MariaDB](https://mariadb.com/) gzipped dump is provided in the root folder.
 
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


### Views
Some support views are provided.



## RDF mapping

The Relational Database is mapped to the Graph Database by means of the [D2RQ platform](http://d2rq.org/).
The mapping file is provided in the ```rdf``` folder.
In the same folder, a gzipped dump file of the triples in turtle format is given.
Triples are produced with the command:

```
./dump-rdf -f TURTLE -o lemmaBank lemmaBankMap.ttl
```

after editing the mapping file specifying the database access parameters.


## Credits

- **Creators**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori
- **Contributors**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori

The _LiLa: Linking Latin_ project has received funding from the European Research Council (ERC) under the European Union's Horizon 2020 research and innovation programme – Grant Agreement No. 769994.

## Copyright
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />These resources are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

## Citation
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4017229.svg)](https://doi.org/10.5281/zenodo.4017229)
