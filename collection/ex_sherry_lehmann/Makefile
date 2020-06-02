#!/usr/bin/make -f
SHELL:=/bin/bash

# c:=fin collection
c:=node ../../../fin-cli/lib collection

col:=ex-sherry-lehmann

catalogs:=$(wildcard ark_87287/d7* )
ttl:=$(patsubst %,%/metadata.ttl,${catalogs})
ldp:=$(patsubst %,%/ldp.ttl,${catalogs})

schema:=http://schema.org

ex:=d7js34

INFO:
	echo catalogs: ${catalogs}


.PHONY: ldp add-graph

ldp: new-collection.ttl ${ldp} ldp.ttl

define indexable
  fin http patch --data-string "INSERT {<> <http://digital.ucdavis.edu/schema#textIndexable> true . } WHERE {}" -P h /collection/$1/$2/fcr:metadata
endef

graph.hdt: graph.ttl
	rdf2hdt $< $@

col_uri:=<https://next.dams.library.ucdavis.edu/fcrepo/rest/collection/${col}>
query:=PREFIX schema: <http://schema.org/>\
PREFIX skos: <http://www.w3.org/2004/02/skos/core\#> \
PREFIX col: ${col_uri} \
PREFIX fast: <http://id.worldcat.org/fast/> \
CONSTRUCT { ?o schema:name ?l } \
WHERE { GRAPH col: { ?s schema:about ?o }	\
OPTIONAL { GRAPH ?t { ?o skos:prefLabel ?l }}}

graph.ttl:jena:=http://localhost:3030/dams/query
graph.ttl:
	http --form ${jena} Accept:text/turtle query='${query}' > $@

new-collection.ttl: agents:= quinn enebeker jrmerz
new-collection.ttl: schema:=http://www.schema.org
new-collection.ttl: index.ttl
	$c delete -f ${col};\
	$c create ${col} index.ttl;\
  $c relation add-container ${col} catalogs -T part;
	fin http get -P b /collection/${col} > $@

# $c acl group add ${col} admins rw $(patsubst %,--agent %@ucdavis.edu,${agents});\

add-graph: d:=http://digital.ucdavis.edu/schema
add-graph: graph.hdt
	fin rm /collection/${col}/graph
	$c resource add ${col} -H "Content-Type: application/octet-stream" $< graph
	$c relation add-properties ${col} ${d}#hasGraph graph ${d}#isGraph

add-workExample: schema:=http://www.schema.org
add-workExample:
	$c relation add-properties ${col} ${schema}/workExample catalogs/${ex}/media/images/${ex}-000 ${schema}/exampleOfWork;\

ldp.ttl: add-workExample
	fin http get -P b /collection/${col} > $@

${ldp}:%/ldp.ttl:%/metadata.ttl
	$c resource delete ${col} catalogs/$(notdir $*);\
	$c resource add ${col} $< catalogs/$(notdir $*);\
	$c relation add-container -T media ${col} catalogs/$(notdir $*)/media;\
	$c resource add --type MediaObject ${col} $*/media/$(notdir $*).pdf catalogs/$(notdir $*)/media/pdf; \
	$c resource add --type MediaObject ${col} image_list.ttl catalogs/$(notdir $*)/media/images;\
  fin http patch --data-string "PREFIX s: <http://schema.org/> PREFIX ldp: <http://www.w3.org/ns/ldp#> INSERT {<> ldp:hasMemberRelation s:hasPart ; ldp:isMemberOfRelation s:partOf; ldp:membershipResource <>. } WHERE {} " /collection/${col}/catalogs/$(notdir $*)/media/images ;\
  for f in $*/media/images/$(notdir $*)-*.jpg; do \
    b=$$(basename $$f .jpg);\
    p=$${b#*-};\
    $c resource add --type MediaObject ${col} $$f catalogs/$(notdir $*)/media/images/$$b;\
    ins="INSERT {<> <http://schema.org/position> $$p } WHERE {}";\
    fin http patch --data-string "$$ins" /collection/${col}/catalogs/$(notdir $*)/media/images/$$b/fcr:metadata ;\
  done;\
	if [[ -e $*/media/$(notdir $*).txt ]]; then \
	  $c resource add --type MediaObject ${col} $*/media/$(notdir $*).txt catalogs/$(notdir $*)/media/text; \
    fin http patch --data-string 'INSERT {<> <http://www.ebu.ch/metadata/ontologies/ebucore/ebucore#filename> "$(notdir $*).txt" . } WHERE {}' /collection/${col}/catalogs/$(notdir $*)/media/text/fcr:metadata ;\
	  $(call indexable,${col},catalogs/$(notdir $*)/media/text);\
	fi;
	fin http get -P b /collection/${col}/catalogs/$(notdir $*) > $@

clean:
	@rm -f ldp.ttl ${ldp}
