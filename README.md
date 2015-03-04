# RESTful Open Annotation API specification

This respository created as a placeholder for materials and discussion related to the specification of a RESTful Open Annotation API. Actual content largely TODO, but there is some preliminary material in the [wiki](https://github.com/restful-open-annotation/spec/wiki).

## Aims / goals / design

* Adhere to Linked Data principles: http://www.w3.org/DesignIssues/LinkedData.html
  * Use dereferenceable HTTP URIs as names for things, return useful information
* Use the Open Annotation (OA) data model http://www.openannotation.org/spec/core/
  * Require support for the OA JSON-LD serialization http://www.openannotation.org/spec/core/publishing.html
  * Allow other RDF serializations
* Stick to existing standards and best practices
* Simple, minimal API
  * Core resources: collection of annotations and individual annotation
* Follow rough consensus on representation: annotations ...
  * are standoff
  * form a graph
  * have arbitrary associated data

## Minimal JSON-LD context

https://gist.github.com/spyysalo/a80389cd0d1a65feaeba

## Stuff to be migrated here

* https://github.com/spyysalo/restful-oa
* https://github.com/spyysalo/restful-oa/wiki
