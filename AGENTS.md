# AGENTS.md

Notes for automated agents and scripts that read the LiLa Lemma Bank. `README.md` describes the
tables; this file lists what is easy to get wrong. Counts are those of commit `1a1ee92` (dump
completed 2026-06-09 14:22:24); they change with each dump.

## What is here

- `lila_db.sql.gz`: a MariaDB 10.5 dump of the relational database (`lila_db`).
- `rdf/lemmaBank.ttl`: the Lemma Bank as Turtle. `rdf/lemmaBankMap.ttl` is the D2RQ mapping,
  `rdf/lemmario_dataset.ttl` the dataset description.
- The maintainers generate the dump and `rdf/lemmaBank.ttl`. If you are not a maintainer, do not
  edit or regenerate them; open an issue instead.

## Loading

- SQL: `gunzip -c lila_db.sql.gz | mariadb -u <user> -p`. The dump runs `CREATE DATABASE IF NOT
  EXISTS lila_db` and `USE lila_db` itself, then creates the tables and views.
- RDF: any Turtle parser (rdflib, Apache Jena, Oxigraph). The file has no blank nodes.
- Check the load: both sources hold 168,792 lemmas (`SELECT COUNT(*) FROM lemma`; subjects typed
  `lila:Lemma`) and 95,844 hypolemmas (`ipolemma`; `lila:Hypolemma`).

## Identifiers

- Lemmas are `http://lila-erc.eu/data/id/lemma/N` (`lemma.id_lemma`), hypolemmas
  `http://lila-erc.eu/data/id/hypolemma/N` (`ipolemma.id_ipolemma`).
- `rdf/lemmaBank.ttl` writes them as prefixed names, `lilaLemma:N` and `lilaIpolemma:N`. A text
  search for the full IRI finds nothing; search for the prefixed name, or parse the file.
- The two namespaces share one number line: 95,110 numbers are both a lemma ID and a hypolemma ID.
  Always key on namespace and number together, never on the number alone.
- Labels are not unique: 9,669 labels belong to more than one lemma or hypolemma, with no
  homograph numbering. Resolve by ID, not by label.

## Deprecations

- Withdrawn IDs are in `deprecatedLemma` (513 rows, 511 IDs), `deprecatedHypolemma` (475) and
  `deprecatedHypoHypolemma` (3), in the SQL dump only. The RDF publishes no deprecation.
- `byLemma` / `byHypolemma` give the successor when there is one, one row per successor, and it may
  be in the other namespace. Check these tables before treating an ID from another resource as
  missing.
- An empty successor does not mean the word is gone: deprecated `lemma/43279` *concutio* names
  none, yet `lemma/95622` *concutio* is live. Search by label and POS as well.

## Easy to misread

- Comparatives and superlatives: `rdf/lemmaBank.ttl` contains all of them (16,647 `COMP`,
  16,677 `SUP`), while the mapping keeps only the 58 listed in `hypolemmaCompSup`. Say which
  source you used.
- `lilaLU` lists paths, not entries: a hypolemma has one row per parent, and 33,269 hypolemmas
  have none. Use `lemma` and `ipolemma` for counts.

## Codes

- The `descr` column is NULL in `flectional_category`, `gender`, `grade`, `plurality` and
  `universal_pos_tag`, and in `source` it repeats the code. The inflection codes are labelled in
  the LiLa ontology: the Turtle links lemmas to them with `lila:hasInflectionType lila:<code>`, and
  `lila:v3r` is "third conjugation verb". Take glosses from there.
- Match codes exactly: `v3` (19 lemmas, "third conjugation verb (isolated form)") is a different
  code from `v3r` (3,527), `v3d` (214) and `v3e` (48). Use `LIKE 'v3%'` to get all four.

## Boundaries

- Always: state the commit or release your figures come from.
- Ask the maintainers (open an issue) before treating a difference between the SQL, the mapping
  and the Turtle as an error in any of them.
- Never, unless you are a maintainer: commit a regenerated dump or Turtle.

## Licence and citation

The data are CC BY-SA 4.0 (`LICENSE`). Cite the paper and the release or commit used, as given in
the Citation section of `README.md`.
