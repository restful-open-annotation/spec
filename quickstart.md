---
layout: base
title: Quickstart
server: http://178.79.168.194
apibase: http://178.79.168.194/api
---

# Quickstart

The RESTful Open Annotation API provides a simple way to share
annotations online. 

As the name suggests, the API is
[REST](http://en.wikipedia.org/wiki/Representational_state_transfer)-style
and based on [Open
Annotation](http://www.openannotation.org/spec/core/). The API
represents resources using [JSON-LD](http://json-ld.org), a standard
extension of the [JSON](http://json.org) format.

Basic annotations are simple:

    {
      "@id": "{{page.apibase}}/annotations/1",
      "target": "http://en.wikipedia.org/wiki/Text_annotation#History"
      "body": "Some history of text annotation",
    }

This page shows how to get started using the API though a series of
examples that you can run with the
[`curl`](http://en.wikipedia.org/wiki/CURL) tool (or similar), or try
out by clicking on links.

#### Contents

* [Read operations](#read-operations)
  * [Get annotation](#get-annotation)
  * [Get collection](#get-collection)
* [Write operations](#write-operations)
  * [Create annotation](#create-annotation)
  * [Update annotation](#update-annotation)
  * [Delete annotation](#delete-annotation)
* [Next steps](#next-steps)

## Read operations

The API defines two endpoints types: an individual annotation, and a
collection of annotations. Both support reading in the usual way,
using the [HTTP GET
method](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods).

(HTTP GET is the method your browser normally uses to read resources
such as web pages, and the URLs below work also in the browser.)

### Get annotation

You can find an example annotation at
<{{page.apibase}}/annotations/1>. Using, `curl`, you can get it with
the following request:

    curl -X GET {{page.apibase}}/annotations/1

Response:

    {
      ...
      "@id": "{{page.apibase}}/annotations/1",
      "body": "Some history of text annotation",
      "target": "http://en.wikipedia.org/wiki/Text_annotation#History"
      ...
    }

(Parts omitted for brevity.) These fields are:

* `@id`: the globally unique identifier of the annotation.
* `body`: the information that the target is annotated with, here a short text string.
* `target`: the thing that is annotated, here a fragment (`#History`) of a Wikipedia article.

Note that the values of `@id` and `target` are HTTP URIs that you can
look up to get useful information.

### Get collection

To get all annotations in a collection, simply issue a GET to the
`annotations/` API endpoint such as <{{page.apibase}}/annotations/>:

    curl -X GET {{page.apibase}}/annotations/

Response:

    {
      "@graph": [
        {
          "@id": "{{page.apibase}}/annotations/1",
          "target": "..."
          ...
        },
        {
          "@id": "{{page.apibase}}/annotations/2",
          "target": "..."
          ...
        },
        ...
      },
      "next": "{{page.apibase}}/api/annotations?page=2"
      "last": "{{page.apibase}}/api/annotations?page=489",
      ...
    }

Some things to note here:

* The data of the annotations in the collection is included.
* The collection is large, so the response contains only part of it.
* Pagination links are included using standard [link relations](http://en.wikipedia.org/wiki/Link_relation) (`next`, `last`, etc.).

If you just need to read annotations, this is all you need to get
started! You may want to have a look at [next steps](#next-steps)
below for more detail, or read on for info on how to create, update
and delete annotations.

## Write operations

Write operations are also defined in the usual way: annotations are
created with the POST method, updated with PUT, and deleted with
DELETE.

### Create annotation

You can create new annotations by POSTing JSON data to an
`annotations/` API endpoint. For example:

    curl -X POST -H 'Content-Type: application/ld+json' -d '
    {
      "@context": "{{page.server}}/ns/restoa.jsonld",
      "target": "http://en.wikipedia.org/wiki/Linked_data"
    }
    ' {{page.apibase}}/annotations

Note the following:

* The format is JSON-LD, and its content type is
[`application/ld+json`](http://www.w3.org/TR/json-ld/#application-ld-json).
* JSON-LD documents require a [context](context.html)
document, here identified using `@context`.
* This annotation has no `body`. Only `target` is required.

The server will respond to the above request with

    {
      ...
      "@id": "{{page.apibase}}/annotations/2",
      "@type": "oa:Annotation",
      "annotatedAt": "2015-03-01T16:27:56",
      "serializedAt": "2015-03-01T16:27:56",
      "target": "http://en.wikipedia.org/wiki/Linked_data",
    }

(The `@id` will have a different number when you run this. Please
substitute the actual `@id` value that you got when running the PUT
and DELETE examples below.)

Note that the server echoes back the annotation, and has added a
number of fields:

* The unique `@id` that can be used to access the annotation via the API.
* `@type`: the annotation type. OA annotations must be instances of `oa:Annotation`.
* [Provenance](http://www.openannotation.org/spec/core/core.html#Provenance)
  information such as `annotatedAt` and `serializedAt`
  (in [ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) format).

Note that not all servers add provenance information, and some may
reject annotations that have no `@type`.

### Update annotation

Previously created annotations can be updated with PUT.

Request:

    curl -X PUT -H 'Content-Type: application/ld+json' -d '
    {
      "@context": "{{page.server}}/ns/restoa.jsonld",
      "target": "http://en.wikipedia.org/wiki/Annotation"
    }
    ' {{page.apibase}}/annotations/2

Response:

    {
      "@id": "{{page.apibase}}/annotations/2",
      "@type": "oa:Annotation",
      "target": "http://en.wikipedia.org/wiki/Annotation",
      "annotatedAt": "2015-03-01T16:27:56",
      "serializedAt": "2015-03-01T17:07:12",
      ...
    }

The server has again filled in information missing from the request,
also updating `serializedAt` to reflect the current time (last
modification time).

### Delete annotation

Annotations can be deleted with DELETE.

Request:

    curl -i -X DELETE {{page.apibase}}/annotations/2

Response:

    HTTP/1.1 204 NO CONTENT

A successful delete produces an empty response with the status code
[204 NO CONTENT](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes#2xx_Success).

## Next steps

For more detail on different parts of the API, you may wish to have a
look at these pages:

* [Data model](data-model.html): introduction to the abstract OA data model
* [JSON-LD](json-ld.html): information on the JSON-LD format
* [Core API](api-core.html): technical specification of the core API
* [Extensions](extensions.html): optional extensions to the core API

Also, the API is discoverable through use: as JSON-LD, all of its
terms resolve to URLs that you can look up for more information.
