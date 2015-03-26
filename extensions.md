---
layout: base
title: API Extensions
---

# API Extensions

The following features may optionally be supported by implementations
of the API.

## Pagination

For large collections, the server may respond with only part of the
collection in response to a `GET` collection request. If it does so,
the server must provide URLs for other parts as part of the
collection, using standard [link
relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml)
in the top level of the response.

Example request:

    curl -i http://example.org/annotations/

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json
    
    {
      "@context": ...
      "@id": "/annotations/",
      "generatedAt": "2012-04-09",
      "@graph":
      [
        ...
      ],
      "start": "/annotations",
      "next":  "/annotations?page=2"
      "last":  "/annotations?page=49402",
    }

## Content negotiation

All conformant implementations must support [JSON-LD](http://json-ld.org) with the [MIME type](http://en.wikipedia.org/wiki/MIME) `application/ld+json`. RESTful Open Annotation stores may also support other formats through standard [content negotiation](http://en.wikipedia.org/wiki/Content_negotiation). Namely, if the client specifies preference for one of the following in an `Accept` header, the store may respond with:

* `text/html`: human-readable representation
* `application/rdf+xml`: [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/) serialization
* `application/n-triples`: [N-triples](http://www.w3.org/TR/n-triples/) serialization (TODO: quads?)
* (TODO more)

(Conversion from JSON-LD to other RDF serializations is supported by the many libraries available e.g. from <http://json-ld.org/>)

(TODO PUT/POST w/Content-Type other than application/ld+json)

## Search

## `PATCH`

## Bulk update

## `DELETE` collection

## Create annotation on `PUT` (upsert)

## ETags, If-Match, etc.

## Document store
