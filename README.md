# LiLa_Lemma-Bank
The LiLa Lemma Bank is core of the LiLa Knowledge Base. It consists of a large collection of
Latin lemmas, serving as the backbone to achieve interoperability between the resources, by linking all those entries in lexical resources and tokens in corpora that point to the same lemma.

Data are coded both in relational database ( SQL format ) and graph database (RDF triples - turptle format ).
Mapping between SQL and RDF data is made by mean of D2RQ according to the mapping file provided in 'rdf' folder.

##  RELATIONAL DATABASE DESCRIPTION

[MariaDB](https://mariadb.com/) gzipped dump is provided in the root folder.
 
### Main tables

* **lemmas** 
```
lemma
```
Each field refers to corrriposnding matedata table:
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
Field `type` refers to the the table
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
a questi lemmi sono attribuiti esplicitamemte i suffissi.



### RDF mapping exception
All hypolemmas are mapped in RDF except the one of type Comparative and Superlative.
Comparative (and superlative) referred in table ```hypolemmaCompSup``` are however mapped.



### LemLat - LiLa Relations

Some lemmas or hypolemmas are related to one or more LemLat lemma:
```
 lemlat_lila        
``` 



### Main look-up a.k.a. table

```
lilaLU
``` 
Materialized View which put together lemmas and hypolemma.
The three field ```id_lemma,id_ipolemma0,id_ipolemma1``` show the 'path' of 
each lemma or hypolemma according to the  ```class```: 
- `0` for  lemma, 
- `1` for  hypolemma of lemma, 
- `2` for  hypolemma of hypolemma

All written representation for each entry is listed in field ```wrList```


### Views
Some support views are provided.



## RDF mapping

Relational Databdes is mapped to Graph Database by means of [D2RQ platform](http://d2rq.org/).
Mapping file is provided in ```rdf``` folder.
In the same folder a gzipped dump file of the triples in turtle format is given.
Treples are produced with the command:

```
./dump-rdf -f TURTLE -o lemmaBank lemmaBankMap.ttl
```

after editing the mapping file specifying database access parameters.


## Credits

- **Creators**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori
- **Contributors**: Marco Passarotti, Flavio Massimiliano Cecchini, Greta Franzini, Eleonora Litta, Francesco Mambrini, Giovanni Moretti, Paolo Ruffolo, Rachele Sprugnoli, Marinella Testori

The _LiLa: Linking Latin_ project has received funding from the European Research Council (ERC) under the European Union's Horizon 2020 research and innovation programme – Grant Agreement No. 769994.

## Copyright
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons Licence" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />These resources are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

## Citation
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.4017229.svg)](https://doi.org/10.5281/zenodo.4017229)
