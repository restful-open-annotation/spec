---
layout: base
title: OA context
---

# Open Annotation context

The **context** defines the meaning of the terms used in a
[JSON-LD](http://json-ld.org) document.

This page touches briefly on how context is used in JSON-LD, and then
presents the specific context used in RESTful Open Annotation
documents. If you are already familiar with JSON-LD, you may want to
jump straight to the [context definition](#full-context-definition).

## Introduction

JSON documents use names such as `parent`, which are short but
ambiguous: for example, *parent* can refer to the mother or father of
a person, or the containing data item in a hierarchical model.

By contrast, Linked Data uses URIs such as
<http://purl.org/vocab/relationship/parentOf> and
<http://sioc-project.org/ontology#term_has_parent> for names. These
are unambiguous, but too verbose for normal human use.

Here's where the context comes in. It allows us to define that when we
write `parent`, we mean `http://purl.org/vocab/relationship/parentOf`.
The context allows us to avoid ambiguity when writing JSON objects
such as

    {
      "parent": "Eve",
      ...
    }

In a bit more technical detail, JSON-LD defines algorithms that
[expand](http://www.w3.org/TR/json-ld-api/#expansion) JSON documents
such as the above with respect to a given context to produce documents
such as

    {
      "http://purl.org/vocab/relationship/parentOf": 
          "http://example.org/people/Eve",
      ...
    }

and [compact](http://www.w3.org/TR/json-ld-api/#compaction) expanded
documents to recover the short forms.

## Core OA context 

RESTful OA uses <span class="hint--top" data-hint="Namely, the W3C Web
Annotation WG version">a version</span> of the recommended
[Open Annotation
context](http://www.openannotation.org/spec/core/publishing.html). The
full context defines names for talking about motivations for
annotating, styling for display, and source document caching, among
other things.

While these are all important issues, we can get started with much
less. Below is a minimal "core" of the context.

<script src="https://gist.github.com/spyysalo/05252c6d818a2143b085.js"></script>

The first line defines `oa` to be a <span class="hint--top"
data-hint="Like a namespace prefix in XML or a CURIE">prefix</span>
corresponding to `http://www.w3.org/ns/oa#`.

The second line then defines `body` to be a
[term](http://www.w3.org/TR/json-ld/#dfn-term) that maps to
the [compact form](http://www.w3.org/TR/json-ld/#dfn-compact-iri)
`oa:hasBody`. Using the definition of the first line, this
form can be expanded to the full URL
[`http://www.w3.org/ns/oa#hasBody`](http://www.w3.org/ns/oa#hasBody).

The third line defines a similar expansion for `target`, adding also
`"@type": "@id"`. This somewhat opaque piece of syntax simply means
that the values of `target` are URLs. This is how JSON-LD adds
hyperlinks to standard JSON, which lacks them.

(See [data model](data-model.html) for definitions of `target` and
`body`.)

## Full context definition

The full context definition for the RESTful Open Annotation API is
available from <http://nlplab.org/ns/TODO>. It is recommended that
systems using the API use this context, for example by referencing it
as follows:

    {
      "@context": "http://nlplab.org/ns/TODO",
      ...
    }

The bulk of this context is simply the [latest
version](http://www.w3.org/TR/2014/WD-annotation-model-20141211/#json-ld-context)
(Dec. 2014) derived from the [Open Annotation
context](http://www.openannotation.org/spec/core/publishing.html) by
the W3C [Web Annotation WG](http://www.w3.org/annotation/), which is
carrying on the OA standardization effort.

The OA context is presented in great detail in [Open
Annotation](http://www.openannotation.org/) and [Web Annotation
WG](http://www.w3.org/annotation/) materials, so we won't give a
detailed explanation here.

Instead, we will describe the modest extensions that the RESTful Open
Annotation API makes to the W3C working draft context. In brief, to
implement the collection pattern frequently seen in RESTful APIs, we
define a prefix for standard [link
relations](http://en.wikipedia.org/wiki/Link_relation)

    "lrel" :  "http://www.iana.org/assignments/link-relations/#"

And define terms matching the standard use of a few link relations, as
follows:

    "prev":          {"@type":"@id", "@id" : "lrel:prev"},
    "next":          {"@type":"@id", "@id" : "lrel:next"},
    "last":          {"@type":"@id", "@id" : "lrel:last"},
    "item":          {"@type":"@id", "@id" : "lrel:item"},
    "collection":    {"@type":"@id", "@id" : "lrel:collection"},

