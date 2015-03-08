---
layout: base
title: Quickstart
---

# Quickstart

The RESTful Open Annotation API provides a simple way to share
annotations online. 

As the name suggests, the API is [REST]()-style and based on [Open
Annotation](http://www.openannotation.org/spec/core/). The API uses
[JSON-LD](http://json-ld.org) to represent resources.

This page shows how to get started using the API though a series of
simple examples that you can run. The only requirement is the
simple [`curl`](http://en.wikipedia.org/wiki/CURL) tool (or similar).

## Get annotation

Request:

    curl http://weaver.nlplab.org/api/annotations/d14a982d45

Response:

    {
      "@id": "http://weaver.nlplab.org/api/annotations/d14a982d45",
      "target": "http://www.gutenberg.org/cache/epub/5200/pg5200.txt#char=50,61"
      "body": "Person"
      ...
    }

(Parts omitted for brevity.) These fields are:

* `@id`: the globally unique identifier of the annotation.
* `target`: the thing that is annotated.
* `body`: the information that the target is annotated with.

Note that the values of `@id` and `target` are HTTP URIs that you can
look up to get useful information.

## Create annotation

Request:

    curl -H 'Content-Type: application/json' \
         -d '{ "target": "http://example.org" }' \
         http://weaver.nlplab.org/api/annotations

Response:

    TODO

Comments:

* TODO: content is echoed, etc.

## Update annotation

Request:

    TODO

Response:

    TODO

Comments:

* TODO

## Delete annotation

Request:

    TODO

Response:

    TODO

Comments:

* TODO

## Get collection

Request:

    curl http://weaver.nlplab.org/api/annotations/

Response:

    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@graph": [
        {
          "@id": "http://weaver.nlplab.org/api/annotations/54f867f50ce",
          "target": "..."
          ...
        },
        {
          "@id": "http://weaver.nlplab.org/api/annotations/54f867f50cd",
          "target": "..."
          ...
        },
        ...
      "next": "http://weaver.nlplab.org/api/annotations?page=2"
      "last": "http://weaver.nlplab.org/api/annotations?page=489",
    }

Some things to note here:

* The data of the annotations in the collection is included.
* The collection is large, so the response contains only part of it.
* Pagination links are included using standard [link relations](http://en.wikipedia.org/wiki/Link_relation).

