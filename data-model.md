---
layout: base
title: Data model
---

# Data model

The abstract data model defines the data items relevant to describing
annotations online and how they relate to each other.

The API uses the core Open Annotation data model, described in detail in the
[OA specification](http://www.openannotation.org/spec/core/core.html).
This page briefly introduces the core model.

## Introduction

From a high-level perspective, the OA model is similar to <span
class="hint--top" data-hint="Annotation graphs, DADA, LAF/GrAF, PAULA,
...">many other</span> related proposals:

* Annotations are **standoff**, that is, separate from annotated
  things, which are identified through references (contrast <span
  class="hint--top" data-hint="Such as HTML.">inline annotation</span>).
* Annotations form a **<span class="hint--top"
  data-hint="Specifically, a labeled directed graph.">graph</span>**
  where the annotations are nodes and can be referred to by other
  annotations.
* Annotations can associate **data** of arbitrary complexity
  with the thing being annotated.

OA uses web and [Linked
Data](http://www.w3.org/DesignIssues/LinkedData.html) technologies.
Most importantly, things (*resources*) are named using HTTP
[URIs](http://en.wikipedia.org/wiki/Uniform_resource_identifier) that
provide useful information when you look them up, and connected with
links that define how they relate to each other.

Many of the technical choices are now fairly obvious:

* Annotations use URIs to identify their <span class="hint--top"
  data-hint="The thing that is annotated.">targets</span>
* Annotations have URIs of their own, so they can be referred to
* The edges of the graph are (typed) links
* Data is represented using web standards such as [RDF](http://www.w3.org/RDF/)

With these choices in place, the specific data model can largely be
defined by the vocabulary of resource and link types and the
constraints on how resources should be linked together.

## Basics, targets and bodies

OA annotations are web resources, so every annotation has a URI as its
identifier. The OA model also requires that every annotation must be
associated with the [`oa:Annotation`](http://www.w3.org/ns/oa#d4e434)
class (or a subclass).  So, all annotations look something like this:

    {
      "@id":   "http://example.org/annotations/1",
      "@type": "oa:Annotation",
      ...
    }

(Examples are in [JSON-LD](json-ld.html), using the OA
[context](context.html), which is assumed to be active. Here, JSON-LD
can be thought of as just [JSON](http://json.org/) with links and some
special keywords like `@id` for "URI".)

Most annotations will typically also have a `target` and a `body`. The
target identifies the thing that is annotated (using its URI), and the
body is the information that the annotation relates to its target.

The `target` is mandatory, but `body` is optional. So, the simplest
possible type of annotation, corresponding roughly to a bookmark,
looks like this:

    {
      "@id":    "http://example.org/annotations/1",
      "@type":  "oa:Annotation",
      "target": "http://example.org/documents/1.txt#char=5,10
    }

(The `#char=5,10` is a [fragment
identifier](http://en.wikipedia.org/wiki/Fragment_identifier) for text
documents.)

The body may be simply a <span class="hint--top" data-hint="In the Web
Annotation WG model.">literal string</span>, so the simplest way an
annotation can associate some information with its target is

    {
      "@id":    "http://example.org/annotations/1",
      "@type":  "oa:Annotation",
      "target": "http://example.org/documents/1.txt#char=5,10,
      "body":   "Person"
    }

Note that here "Person" is just a string of characters and its
specific meaning can only be understood in an application-specific
context. To make the meaning of body references unambiguous, it's
strongly recommended to use URIs, such as

    {
      "@id":    "http://example.org/annotations/1",
      "@type":  "oa:Annotation",
      "target": "http://example.org/documents/1.txt#char=5,10,
      "body":   { "@id": "http://xmlns.com/foaf/0.1/Person" }
    }

(As noted above, `@id` is the JSON-LD keyword for URI.)

With a URI body, it's already possible to associate data of any
complexity with a target. However, JSON-LD also allows data to be
[embedded](http://www.w3.org/TR/json-ld/#embedding) directly:

    {
      "@id":    "http://example.org/annotations/1",
      "@type":  "oa:Annotation",
      "target": "http://example.org/place/white-house,
      "body": {
        "foaf:homepage":    "http://www.whitehouse.gov/",
        "schema:address":   "1600 Pennsylvania Avenue",
        "schema:telephone": "+1-202-456-1414",
      }
    }

`@id`, `@type`, `target` and `body` are the core of the model, and
knowing these (and [`@context`](context.html)) is enough to use and
develop for the RESTful OA API. The following sections on this page
describe additional details of the OA model.

## Provenance

Open Annotation defines a way for annotations to record information
about their origin, as in the following example:

    {
      ...
      "target":      "http://example.org/documents/1.txt",
      "annotatedBy": "http://example.org/users/anon",
      "annotatedAt": "2015-03-01T09:00"
    }

(From here on, we will mostly not show `@id` and `@type` for brevity,
but these should be understood to be always present.)

This is pretty self-explanatory: the annotation was created by the
person (or software) identified by `http://example.org/users/anon` at
9AM on March 1st 2015. (Times must be in the
[xsd:dateTime](http://www.w3.org/TR/xmlschema-2/#dateTime) format.)

In more detail, the OA model defines four terms for recording who (or
what) created each annotation and when: `annotatedBy`, `annotatedAt`,
`serializedBy` and `serializedAt`.

The `By` terms identify the person or software that created the
annotation (`annotatedBy`) and the one that created the data
representation of the annotation (`serializedBy`). Similarly, the `At`
terms identify when the annotation was first created and when it was
(last) represented in its current form.

None of the provenance terms are mandatory. However, systems must
preserve this information when it is present in annotations that they
process.

## Motivations

OA proposes to identify the reasons why an annotation was created
using a mechanism called *motivations*. This information can help
decide how to best interpret an annotation, for example for presenting
to a user.

Consider, for example, the following two annotations.

    {
      ...
      "target":     "http://example.org/documents/1.txt#char=5,10",
      "body":       "Person",
    },
    {
      ...
      "target":     "http://example.org/documents/1.txt#char=5,10",
      "body":       "Typo",
    }

Both have identical structure and string literal bodies, meaning that
a system that doesn't understand what "Person" and "Typo" mean cannot
differentiate between them.

Assigning a motivation to these annotations from the provides a way to
differentiate:

    {
      ...
      "target":     "http://example.org/documents/1.txt#char=5,10",
      "body":       "Person",
      "motivation": "oa:classifying"
    },
    {
      ...
      "target":     "http://example.org/documents/1.txt#char=5,10",
      "body":       "Typo",
      "motivation": "oa:commenting"
    }

A system receiving these annotations does not need to try to
understand an ambiguous, open-ended set of natural language strings
("Person", "Typo", etc.) but only the small [OA inventory of
motivations](http://www.openannotation.org/spec/core/core.html#Motivations).

With this information, a system for visualizing annotations (for
example) could present the former annotation with a shaded background
color shared by all annotations with the "Person" class and the latter
as a speech bubble that shows the comment ("Typo") on mouse-over.

(Note that the OA model does allow also conventional subclassing of
`oa:Annotation`, so that the above information could instead be
captured with `"@type": "oax:Classification"` and `"@type":
"oax:Comment "`, but such classes are [not currently
standardized](http://www.openannotation.org/spec/extension/).)

## Non-core modules

The OA specification differentiates between the *core* aspects of the
data model, presented above, and *modules* that add
functionality. These non-core modules include support for [selecting
any part of structured
documents](http://www.openannotation.org/spec/core/specific.html),
specifying [annotation rendering
styles](http://www.openannotation.org/spec/core/specific.html#Style)
and representing [alternative
annotations](http://www.openannotation.org/spec/core/multiplicity.html#Choice).

We have aimed to keep the RESTful OA API as simple as possible to
assure that fully conformant clients and servers are easy to
implement. For this reason, non-core OA modules are currently
*excluded* from the scope of this specification, and implementations
of the RESTful OA API do not need to support them.
