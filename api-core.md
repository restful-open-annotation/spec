---
layout: base
title: Core API
---

# Core API

This page presents the *core* RESTful Open Annotation API that must be
supported by all conforming implementations. Optional features are
presented on the [extensions page](extensions.html).

## Overview

The core API defines two resouce types, the collection resource
`annotations` and individual `annotation` resources contained in these
collections. These resources can be manipulated using [HTTP
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
requests specify that it is preferred (using the `Accept` header).

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

## GET annotation

Retrieve representation of an annotation.

Response: `200 OK` and complete representation of annotation.

Example request:

    curl -i http://example.org/annotations/123

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json

    {
      "@context": "/ns/restoa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/123",
      "target": "/documents/456#char=42,48",
      "body": "Person",
    }

Notes:

* Relative URLs such as `/annotations/123` are allowed. Relative
  annotations are interpreted relative to the request base URL.
* Servers are strongly recommended to establish exactly one canonical
  URL for each resource and only use relative URLs when the request
  base matches a prefix of a canonical URL.

## PUT annotation

Update an annotation.

Request: representation of annotation.

Response: `200 OK` and complete representation of annotation. 

Example request:

    curl -X PUT -i -H 'Content-Type: application/json' -d '
    {
      "@context": "/ns/restoa.jsonld",
      "@type": "oa:Annotation",
      "target": "/documents/456#char=42,48",
      "body": "Organization",
    }
    ' http://example.org/annotations/123

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json
    
    {
      "@context": "/ns/restoa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/123",
      "target": "/documents/456#char=42,48",
      "body": "Organization",
      "annotatedBy": [ "/users/foo", "/users/bar" ]
    }

Notes:

* `@id` is optional. If provided, it must match the request URL. If not,
  the server must fill it in based on the request URL for the response.
* The server may modify the submitted representation, for example by
  filling in `@id` or `annotatedBy`.

## DELETE annotation

Permanently remove a single annotation.

Response: `204 No Content`

Example request:

    curl -X DELETE http://example.org/annotations/123

Example response:

    HTTP/1.0 204 No Content

## GET collection

Retrieve representation of an annotation collection and all annotations contained in the collection.

Response: `200 OK` and collection and annotations in JSON-LD. The collection is represented as a [named graph](http://www.w3.org/TR/json-ld/#named-graphs).

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
          "target": "/documents/456#char=42,48",
          "body": "Person",
          ...
        },
        {
          "@id": "/annotations/789",
          "target": "/documents/456#char=56,64",
          "body": "Organization",
          ...
        },
      ]
    }

Notes:

* Pagination may optionally be supported. See
  [extensions](extensions.html#pagination).

## POST collection

Create annotation.

Request: representation of annotation.

Response: `201 CREATED` and complete representation of annotation.

Example request:

    curl -X POST -i -H 'Content-Type: application/json' -d '
    {
      "@context": "/ns/restoa.jsonld",
      "@type": "oa:Annotation",
      "target": "/documents/456#char=42,48",
      "body": "Person",
    }
    ' http://example.org/annotations/789

Example response:

    HTTP/1.0 200 OK
    Content-Type: application/ld+json
    
    {
      "@context": "/ns/restoa.jsonld",
      "@type": "oa:Annotation",
      "@id": "/annotations/789",
      "annotatedAt": "2015-03-03T19:16:32+09:00",
      "annotatedBy": "/users/smp",
      "target": "/documents/456#char=42,48",
      "body": "Person"
    }

Notes:

* Clients may optionally provide an `@id` representing a preferred URL
  for the new annotation. Servers must allow but may ignore
  client-provided `@id` values.

## Extensions

For optional extensions to this core API, see the
[extensions page](extensions.html).