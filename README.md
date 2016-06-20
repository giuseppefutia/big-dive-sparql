# SPARQL crash course
Crash course on the SPARQL query language within the Big Dive training program.

Contents published on this repository are based on the W3C Candidate Recommendation “SPARQL Query Language for RDF” from http://www.w3.org/TR/rdf-sparql-query/.

## SPARQL main idea: graph pattern matching
* Describe subgraphs of the queried RDF graph.
* Subgraphs that match your description yield a result.

## SPARQL basics

* SPARQL variables start with a ? and can match any node (resource or literal) in the RDF dataset.
* Triple patterns are just like triples, except that any of the parts of a triple can be replaced with a variable (pattern matching).
* Variables named after the SELECT keyword are the variables that will be returned as results (~SQL).
* The SELECT result clause returns a table of variables and values that satisfy the query.

## SPARQL query basic structure

```
# Prefixes declaration
PREFIX foo: <http://example.com/resources/>
...
# Graph definition
FROM ...
# Results settings
SELECT ...
# Query
WHERE {
    ...
}
# Modifiers
ORDER BY ...
```

## Endpoint used for this crash course
* DBpedia ([Site project](http://dbpedia.org/), [SPARQL endpoint](http://dbpedia.org/sparql)).

## Running our first query

```
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

SELECT ?label
WHERE {
   <http://dbpedia.org/resource/Stanley_Kubrick> rdfs:label ?label
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=PREFIX+rdfs%3A+%3Chttp%3A%2F%2Fwww.w3.org%2F2000%2F01%2Frdf-schema%23%3E%0D%0A%0D%0ASELECT+DISTINCT+%3Flabel%0D%0AWHERE+%7B%0D%0A+++%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+rdfs%3Alabel+%3Flabel+.%0D%0A%7D&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

### Excercise
* Get all properties and all objects related to the <http://dbpedia.org/resource/Stanley_Kubrick> resource.

## Multiple triple patterns and graph traversal

```
SELECT ?movie ?distributor
WHERE {
    ?movie <http://dbpedia.org/ontology/director> <http://dbpedia.org/resource/Stanley_Kubrick> .
    ?movie <http://dbpedia.org/ontology/distributor> ?distributor .
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=%0D%0ASELECT+%3Fmovie+%3Fdistributor%0D%0AWHERE+{%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+.%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdistributor%3E+%3Fdistributor+.%0D%0A}%0D%0A&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

### Excercise
* Get the founders and the founding date of all film distribution houses of movies directed by Kubrick. 

## SPARQL modifiers
The objective of modifiers is slicing, ordering, and otherwise rearranging query results.

* **DISTINCT**
* **LIMIT**
* **OFFSET**
* **ORDER BY**

``` 
SELECT DISTINCT ?director ?directorLabel
WHERE {
    ?movie <http://dbpedia.org/ontology/director> ?director .
    ?director rdfs:label ?directorLabel .
} ORDER BY ASC(?directorLabel) LIMIT 50 OFFSET 200
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=SELECT+DISTINCT+%3Fdirector+%3FdirectorLabel%0D%0AWHERE+{%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Fdirector+.%0D%0A++++%3Fdirector+rdfs%3Alabel+%3FdirectorLabel+.%0D%0A}+ORDER+BY+ASC%28%3FdirectorLabel%29+LIMIT+50+OFFSET+200%0D%0A&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

## SPARQL filters
FILTER constraints use boolean conditions to filter out unwanted query results.

```
SELECT DISTINCT ?director ?directorLabel
WHERE {
    ?movie <http://dbpedia.org/ontology/director> ?director .
    ?director rdfs:label ?directorLabel .
    FILTER (langMatches(lang(?directorLabel), "EN")) .
} LIMIT 50
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=SELECT+DISTINCT+%3Fdirector+%3FdirectorLabel%0D%0AWHERE+{%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Fdirector+.%0D%0A++++%3Fdirector+rdfs%3Alabel+%3FdirectorLabel+.%0D%0A++++FILTER+%28langMatches%28lang%28%3FdirectorLabel%29%2C+%22EN%22%29%29+.%0D%0A}+LIMIT+50&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

### Built-in filters

* Logical: !, &&, ||
* Math: +, -, *, /
* Comparison: =, !=, >, <, ...
* SPARQL tests: isURI, isBlank, isLiteral, bound
* SPARQL accessors: str, lang, datatype
* Other: sameTerm, langMatches, regex

## OPTIONAL pattern
OPTIONAL tries to match a graph pattern, but doesn't fail the whole query if the optional match fails.

```
SELECT DISTINCT ?director ?directorLabel ?quote
WHERE {
    ?movie <http://dbpedia.org/ontology/director> ?director .
    ?director rdfs:label ?directorLabel .
    ?director <http://dbpedia.org/property/quote> ?quote . 
    FILTER (langMatches(lang(?directorLabel), "EN")) .
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=SELECT+DISTINCT+%3Fdirector+%3FdirectorLabel+%3Fquote%0D%0AWHERE+%7B%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Fdirector+.%0D%0A++++%3Fdirector+rdfs%3Alabel+%3FdirectorLabel+.%0D%0A++++%3Fdirector+%3Chttp%3A%2F%2Fdbpedia.org%2Fproperty%2Fquote%3E+%3Fquote+.+%0D%0A++++FILTER+%28langMatches%28lang%28%3FdirectorLabel%29%2C+%22EN%22%29%29+.%0D%0A%7D%0D%0A&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

```
SELECT DISTINCT ?director ?directorLabel ?quote
WHERE {
    ?movie <http://dbpedia.org/ontology/director> ?director .
    ?director rdfs:label ?directorLabel .
    OPTIONAL {?director <http://dbpedia.org/property/quote> ?quote} . 
    FILTER (langMatches(lang(?directorLabel), "EN")) .
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=SELECT+DISTINCT+%3Fdirector+%3FdirectorLabel+%3Fquote%0D%0AWHERE+%7B%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Fdirector+.%0D%0A++++%3Fdirector+rdfs%3Alabel+%3FdirectorLabel+.%0D%0A++++OPTIONAL+%7B%3Fdirector+%3Chttp%3A%2F%2Fdbpedia.org%2Fproperty%2Fquote%3E+%3Fquote%7D+.+%0D%0A++++FILTER+%28langMatches%28lang%28%3FdirectorLabel%29%2C+%22EN%22%29%29+.%0D%0A%7D%0D%0A&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

### Exercise
* Get all directors in DBpedia. Get all movies in which these directors had the role of producers.

## ASK, DESCRIBE, CONSTRUCT
In addition to the SELECT clause, you can specify other keywords depending on the result we want to achieve with our queries.

```
ASK WHERE {
   <http://dbpedia.org/resource/Quentin_Tarantino> <http://dbpedia.org/ontology/birthDate> ?qtBirthDate .
   <http://dbpedia.org/resource/Stanley_Kubrick> <http://dbpedia.org/ontology/birthDate> ?skBirthDate .
   FILTER(?skBirthDate < ?qtBirthDate) .
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=ASK%0D%0AWHERE+%7B%0D%0A+++%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FQuentin_Tarantino%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2FbirthDate%3E+%3FqtBirthDate+.%0D%0A+++%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2FbirthDate%3E+%3FskBirthDate+.%0D%0A+++FILTER%28%3FskBirthDate+%3C+%3FqtBirthDate%29+.%0D%0A%7D&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)


```
DESCRIBE ?movie {
   ?movie <http://dbpedia.org/ontology/director> <http://dbpedia.org/resource/Stanley_Kubrick> .
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=%0D%0A%0D%0ADESCRIBE+%3Fmovie+%7B%0D%0A+++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+.%0D%0A%7D&format=text%2Fturtle&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)


```
PREFIX mo: <http://myontology.org/>

CONSTRUCT { 
  <http://dbpedia.org/resource/Stanley_Kubrick> mo:workWithDistributor ?distributor .
}
WHERE { 
    ?movie <http://dbpedia.org/ontology/director> <http://dbpedia.org/resource/Stanley_Kubrick> .
    ?movie <http://dbpedia.org/property/distributor> ?distributor .
}
```
[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=PREFIX+mo%3A+%3Chttp%3A%2F%2Fmyontology.org%2F%3E%0D%0A%0D%0ACONSTRUCT+%7B+%0D%0A++%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+mo%3AworkWithDistributor+%3Fdistributor+.%0D%0A%7D%0D%0AWHERE+%7B+%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+.%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fproperty%2Fdistributor%3E+%3Fdistributor+.%0D%0A%7D&format=text%2Fturtle&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

## Advanced SPARQL technique: Negation

```
SELECT DISTINCT ?movie
WHERE {
    ?movie <http://dbpedia.org/ontology/director> <http://dbpedia.org/resource/Stanley_Kubrick> .
    ?movie <http://dbpedia.org/property/distributor> ?distributor .
    OPTIONAL {<http://dbpedia.org/resource/Blade_Runner> <http://dbpedia.org/property/distributor> ?badDistributor . FILTER (?distributor = ?badDistributor) .} .
    FILTER ( !BOUND(?badDistributor) )
}
```

[Perform the query](http://dbpedia.org/sparql?default-graph-uri=http%3A%2F%2Fdbpedia.org&query=SELECT+DISTINCT+%3Fmovie%0D%0AWHERE+%7B%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fontology%2Fdirector%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FStanley_Kubrick%3E+.%0D%0A++++%3Fmovie+%3Chttp%3A%2F%2Fdbpedia.org%2Fproperty%2Fdistributor%3E+%3Fdistributor+.%0D%0A++++OPTIONAL+%7B%3Chttp%3A%2F%2Fdbpedia.org%2Fresource%2FBlade_Runner%3E+%3Chttp%3A%2F%2Fdbpedia.org%2Fproperty%2Fdistributor%3E+%3FbadDistributor+.+FILTER+%28%3Fdistributor+%3D+%3FbadDistributor%29+.%7D+.%0D%0A++++FILTER+%28+%21BOUND%28%3FbadDistributor%29+%29%0D%0A%7D&format=text%2Fhtml&CXML_redir_for_subjs=121&CXML_redir_for_hrefs=&timeout=30000&debug=on)

## Advanced SPARQL technique: Sum

```
PREFIX pc: <http://purl.org/procurement/public-contracts#>
PREFIX payment: <http://reference.data.gov.uk/def/payment#>

SELECT SUM(?amount) as ?paidTotal ?company
WHERE {
    SELECT DISTINCT ?contract ?amount ?company
    WHERE {
        ?company <http://purl.org/goodrelations/v1#vatID> "04145300010".
        ?bid pc:bidder ?company .
        ?contract pc:awardedTender ?bid .
        ?contract payment:payment ?payment . 
        ?payment payment:netAmount ?amount .
    }
}
```
[Perform the query](http://public-contracts.nexacenter.org/sparql?default-graph-uri=&query=PREFIX+pc%3A+%3Chttp%3A%2F%2Fpurl.org%2Fprocurement%2Fpublic-contracts%23%3E%0D%0APREFIX+payment%3A+%3Chttp%3A%2F%2Freference.data.gov.uk%2Fdef%2Fpayment%23%3E%0D%0A%0D%0ASELECT+SUM%28%3Famount%29+as+%3FpaidTotal+%3Fcompany%0D%0AWHERE+%7B%0D%0A++++SELECT+DISTINCT+%3Fcontract+%3Famount+%3Fcompany%0D%0A++++WHERE+%7B%0D%0A++++++++%3Fcompany+%3Chttp%3A%2F%2Fpurl.org%2Fgoodrelations%2Fv1%23vatID%3E+%2204145300010%22.%0D%0A++++++++%3Fbid+pc%3Abidder+%3Fcompany+.%0D%0A++++++++%3Fcontract+pc%3AawardedTender+%3Fbid+.%0D%0A++++++++%3Fcontract+payment%3Apayment+%3Fpayment+.+%0D%0A++++++++%3Fpayment+payment%3AnetAmount+%3Famount+.%0D%0A++++%7D%0D%0A%7D&should-sponge=&format=text%2Fhtml&timeout=0&debug=on)

An Italian version of this course is available at: https://github.com/giuseppefutia/sparql-course
