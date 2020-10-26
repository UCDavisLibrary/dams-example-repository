#! /usr/bin/make -f
SHELL:=/bin/bash

inf4:=~/inf4/inf4
col:=$(shell eval `${inf4} util.config` && echo $${INF4[source_collection]})

$(warning ${col})

all: ${col}/graph.hdt

${col}/graph.hdt: graph.ttl
	rdf2hdt $< $@
