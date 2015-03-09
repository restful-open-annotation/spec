---
layout: base
title: API details
---

# API details

This page presents the core RESTful Open Annotation API.

This documentation is fairly technical, and should not be necessary
for using the API, which is simple and self-documenting. (You might
want to see the [quickstart](quickstart.html) document instead.)

On the other hand, if you want to implement a RESTful OA server using
the API, you'll probably need all the detail below.

## Overview

The core API defines a single collection resource, `annotations`. This
collection and the individual annotation resources it contains can be
manipulated using [HTTP
methods](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)
in the standard way:

<table>
<tr><th>Method</th><th>Resource</th><th></th></tr>
<tr><td>GET</td><td>Annotation</td><td>Retrieve annotation</td></tr>
<tr><td>GET</td><td>Collection</td><td>Retrieve all annotations</td></tr>
<tr><td>PUT</td><td>Annotation</td><td>Update annotation</td></tr>
<tr><td>DELETE</td><td>Annotation</td><td>Delete annotation</td></tr>
<tr><td>POST</td><td>Collection</td><td>Create annotation</td></tr>
</table>

The GET operations for annotations and annotation collections must be
implemented by all servers implementing the API. *Read-only* services
may respond with `405 Method Not Allowed` to requests using the other
methods.

The default content type is `application/ld+json`, and all servers
must accept JSON-LD data and provide responses in JSON-LD when
requests specify that it is preferred.

The JSON-LD context defined in the [context
documentation](context.html) is used to represent both annotations and
collections, and responses are
[compacted](http://www.w3.org/TR/json-ld-api/#compaction) with respect
to this context by default.

The following status codes apply across methods:

* `400 Bad Request` on any client error, such as malformed JSON
* `404 Not Found` if a referenced resource does not exist.
* `405 Method Not Allowed` to any undefined method/resource combination,
such as DELETE for a collection.

The method / resource combinations are specified in detail below.

TODOs:

* consider defining PATCH, bulk update, and/or DELETE collection

## GET annotation

Retrieve representation of an annotation.

Response: `200 OK` and complete representation of annotation.

Example request:

    curl -i http://example.org/annotations/123

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json

    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/123",
      "target": "/documents/456.txt#char=42,48"
      "body": "Person",
    }

TODOs: 

* `@context` is OA, but keys are Web Annotation WG in examples

## PUT annotation

Update an annotation.

Request: representation of annotation.

Response: `200 OK` and complete representation of annotation. 

Example request:

    curl -X PUT -i -H 'Content-Type: application/json' -d '
    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@type": "oa:Annotation",
      "target": "/documents/456.txt#char=42,48"
      "body": "Organization",
    }
    ' http://example.org/annotations/123

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json
    
    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/123",
      "target": "/documents/456.txt#char=42,48"
      "body": "Organization",
      "annotatedBy": [ "/users/smp", "/users/to" ]
    }

Notes:

* The server may modify the submitted representation, for example by
  filling in `@id` or `annotatedBy`.

TODOs: 

* consider allowing PUT to create an annotation if it does not exist.

## DELETE annotation

Permanently remove single annotation.

Response: `204 No Content`

Example request:

    curl -X DELETE http://example.org/annotations/123

Example response:

    HTTP/1.0 204 No Content

## GET collection

Retrieve representation of annotation collection and all annotations
contained in the collection. The collection is represented as a
JSON-LD [named graph](http://www.w3.org/TR/json-ld/#named-graphs).

Response: `200 OK` and collection and annotations in JSON-LD.

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
        {
          "@id": "/annotations/123",
          "target": "/documents/456.txt#char=42,48"
          "body": "Person",
          ...
        },
        {
          "@id": "/annotations/789",
          "target": "/documents/456.txt#char=56,64"
          "body": "Organization",
          ...
        },
      ]
    }

For large collections, the server may return only part of the
collection, providing URLs for other parts as part of the top-level
collection object, using standard [link
relations](http://www.iana.org/assignments/link-relations/link-relations.xhtml).

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
      ]
      "start": "/annotations",
      "next":  "/annotations?page=2"
      "last":  "/annotations?page=49402",
    }

TODOs:

* consider defining a way to query annotations e.g. by target document.

## POST collection

Create annotation.

Request: representation of annotation.

Response: `201 CREATED` and complete representation of annotation.

Example request:

    curl -X POST -i -H 'Content-Type: application/json' -d '
    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@type": "oa:Annotation",
      "target": "/documents/456.txt#char=42,48"
      "body": "Person",
    }
    ' http://example.org/annotations/789

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json
    
    {
      "@context": "http://www.w3.org/ns/oa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/789",
      "annotatedAt": "2015-03-03T19:16:32+09:00",
      "annotatedBy": "/users/smp",
      "target": "/documents/456.txt#char=42,48"
      "body": "Person",
    }

# Content negotiation

All conformant implementations must support [JSON-LD](http://json-ld.org) with the [MIME type](http://en.wikipedia.org/wiki/MIME) `application/ld+json`. RESTful Open Annotation stores may also support other formats through standard [content negotiation](http://en.wikipedia.org/wiki/Content_negotiation). Namely, if the client specifies preference for one of the following in an `Accept` header, the store may respond with:

* `text/html`: human-readable representation
* `application/rdf+xml`: [RDF/XML](http://www.w3.org/TR/REC-rdf-syntax/) serialization
* `application/n-triples`: [N-triples](http://www.w3.org/TR/n-triples/) serialization (TODO: quads?)
* (TODO more)

(Conversion from JSON-LD to other RDF serializations is supported by the many libraries available e.g. from <http://json-ld.org/>)

(TODO PUT/POST w/Content-Type other than application/ld+json)
