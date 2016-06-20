# SPARQL crash course
Crash course on the SPARQL query language within the Big Dive training program

Contents published on this repository are based on the W3C Candidate Recommendation “SPARQL Query Language for RDF” from http://www.w3.org/TR/rdf-sparql-query/ 

## SPARQL main idea: graph pattern matching
* Describe subgraphs of the queried RDF graph.
* Subgraphs that match your description yield a result.

## SPARQL basics

* SPARQL variables start with a ? and can match any node (resource or literal) in the RDF dataset.
* Triple patterns are just like triples, except that any of the parts of a triple can be replaced with a variable (pattern matching).
* Variables named after the SELECT keyword are the variables that will be returned as results. (~SQL)
* The SELECT result clause returns a table of variables and values that satisfy the query.

## Running our first query
TODO

A different version of this course is available at: https://github.com/giuseppefutia/sparql-course
