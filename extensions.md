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

<!--
(TODO PUT/POST w/Content-Type other than application/ld+json)
-->

## Suggested extensions

The following extensions have been suggested but no specification has
yet been provided.

* Annotations as sub-collection of document collection
* Search specification using [URI
  templates](https://tools.ietf.org/html/rfc6570)
* HTTP `PATCH` support using [JSON Patch](https://tools.ietf.org/html/rfc6902)
* Bulk update using `POST` and `@graph` instead of individual annotation
* Annotation collection deletion using `DELETE` for collection resource
* Allow annotations to be created on `PUT` (upsert)
* Optimistic concurrency control using
  [ETags](http://en.wikipedia.org/wiki/HTTP_ETag)
* Minimal RESTful document store specification
* Structured error returns following e.g [JSON
  API](http://jsonapi.org/format/#errors) or
  [Eve](http://python-eve.org/features.html#data-validation)
