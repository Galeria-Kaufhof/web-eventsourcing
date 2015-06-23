---
title: Web Event Sourcing - A Web-based protocol for event sourcing
abbrev: Web Event Sourcing
docname: draft-kiessling-web-eventsourcing-00
date: 2015
category: info

ipr: trust200902
area: General
workgroup: 
keyword: Internet-Draft

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
 -
    ins: M. Kiessling
    name: Manuel Kiessling
    organization: GALERIA Kaufhof
    email: manuel.kiessling@kaufhof.de
    uri: http://www.galeria-kaufhof.de
 -
    ins: J. Algermissen
    name: Jan Algermissen
    organization: 
    email: algermissen@acm.org
    uri: http://www.jalg.net/
    
normative:
  RFC5005:
  RFC4229:
  RFC7231:
  
  
informative:
  RFC5789:
  I-D.snell-http-prefer:
  ResourceSync:
    target: http://www.openarchives.org/rs
    title: ResourceSync Framework Specification
    author:
      - ins: TBD
      - organization: TBD
  apps-discuss:
    target: https://www.ietf.org/mailman/listinfo/apps-discuss
    title: IETF Apps-Discuss Mailing List
    author:
      - organization: IETF

--- abstract

This document describes HTTP based event sourcing.

--- note_Note_to_Readers

This draft should be discussed on the apps-discuss mailing list; see
{{apps-discuss}}.
         
--- middle

Introduction
============

TBD

Open Issues
===========

- We could sacrifice Event-Type extensibility to make the Event-Type
  header more easy to process and just define the value as any
  unsafe HTTP method.

- We should define a specialized multipart subtype to express the
  requirement to include a Content-ID header in the parts. For
  example multipart/events or multipart/http-requests.
  (The early multipart/package type did something like that IIRC)

- I do not like that the extensibility of the Event-Type header
  does not include a reference mechanism to 'follow your nose'

Notational Conventions
----------------------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in 


Event Sourcing on The Web
=========================

TBD


Snapshots
=========

TBD


Snapshot Index Syntax Example
-----------------------------


{
  "uris": [
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/0",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/1",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/2",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/3",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/4",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/5",
    "/resourcesync/product/snapshot/2015-06-23T00:36:45.885Z/6"
  ],
  "completed": 1435019872400,
  "started": 1435019805885,
  "id": "2015-06-23T00:36:45.885Z"
}



Snapshot Part Syntax Example
----------------------------


    HTTP/1.1 200 OK
    Server: Tengine/2.1.0
    Date: Tue, 23 Jun 2015 07:15:47 GMT
    Content-Type: multipart/package; boundary="_-------------1435019846753"
    Content-Length: 3159122
    Connection: keep-alive
    
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /products/16325906:001:99999
    ETag: W/"12345678"
    Content-Length: 93
    
    {"id":"16325906:001:99999","assets":[{"id":"930770","role":"spot"},{"id":"9424","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16326538:001:99999
    ETag: W/"12345678"
    Content-Length: 93
    
    {"id":"16326538:001:99999","assets":[{"id":"931862","role":"spot"},{"id":"9425","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16326590:001:99999
    ETag: W/"12345678"
    Content-Length: 93
    
    {"id":"16326590:001:99999","assets":[{"id":"914839","role":"spot"},{"id":"9408","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16327887:001:99999
    ETag: "12345678"
    Content-Length: 193
    
    {"id":"16327887:001:99999","assets":[{"id":"9517","role":"2"},{"id":"919130","role":"spot"},{"id":"9518","role":"1"},{"id":"9514","role":"5"},{"id":"9515","role":"3"},{"id":"9516","role":"4"}]}
    --_-------------1435019846753--
    

Change Event Feeds
==================

TBD

- Define the term `event target`.
- Define the term `event part`.


The Event-Type HTTP Header
--------------------------

This specification defines the `Event-Type` HTTP header to provide consumers
of change event feeds with information about the nature of the event regarding
the event target.

The `Event-Type` header takes as its value a key-value pair with the key
indicating the event type family and the value indicating the event type
name from the family.


Event feed publishers SHOULD only use events from a single family in a given
feed.

The Event Type family `http-equiv`
----------------------------------

This specification defines the event type family 'http-equiv'. The allowed
value set for this family is the set of unsafe HTTP methods, such as PUT,
PATCH, DELETE, POST.

Consumers of `http-equiv`-typed events MUST process the event in the same
way as they would process an HTTP request equivalent to the event part and
HTTP method.

    Event-Type: http-equiv=PUT


TBD: Provide example of a single part and describe processing.



Feed Page Syntax Example
------------------------


    GET /products/changes/latest
    Accept: multipart/mixed
  
    200 Ok
    Last-Modified: Sat, 11 Jan 2014 05:00:13 GMT
    ETag: "abc"
    Cache-Control: no-cache
    Link: </products/changes/2013/01/01/16>; rel="prev"
    Content-Type: multipart/mixed; boundary="gc0p4Jq0M:2Yt08jU534c0p"
 
    --gc0p4Jq0M:2Yt08jU534c0p
    Link: </products/7628827272>;rel="about"
    Event-Type: http-equiv=PUT
    Content-Type: application/vnd.gkh.product+json
    Last-Modified: Sat, 09 Jan 2014 15:02:23 GMT
    Content-ID: <1234@products.example.org>
    Content-Length: nnn
      
    {"id":"..." , ... }
    --gc0p4Jq0M:2Yt08jU534c0p
    Link: </products/7623288273>;rel="about"
    Event-Type: http-equiv=PATCH
    Content-Type: application/vnd.gkh.product-patch+xml
    Last-Modified: Sat, 09 Jan 2014 15:02:23 GMT
    Content-ID: <567@products.example.org>
    Content-Length: nnn
     
    <product-patch>
      <set-availability>AVAILABLE</set-availability>
    </product-patch>
    --gc0p4Jq0M:2Yt08jU534c0p
    Link: </products/3338827272>;rel="about"
    Event-Type: http-equiv=DELETE
    Last-Modified: Sat, 09 Jan 2014 15:02:23 GMT
    Content-ID: <899919@products.example.org>
    Content-Length: 0
      
    --gc0p4Jq0M:2Yt08jU534c0p--
    
    
    

Publishing
==========

Publishing Snapshots
--------------------

TBD

Publishing Feeds
----------------

TBD
- explain 'latest'
- explain prev and pre-archive and switching to prev-archive
  (analog https://tools.ietf.org/html/rfc5005) - Make 5005 normative
- 

Consuming
=========

Consuming Snapshots
-------------------

TBD
- Describe when and how to process the feed afterwards. See also below.

Consuming Feeds
---------------

TBD
TBD

    "Eine Snapshot-Generierung startet um T1 und endet um T2
    Beide Zeitpunkte stehen im Snapshot-index
    
    Client startet Snapshot-Konsum um T3 und beendet Snapshot Konsum um T4
    
    Mit T1 < T2 < T3 < T4
    
    Client muss nun ab einschliesslich T1 den Feed lesen, um zu ermitteln,
    welche ‘items’ sich während der Snapshot-Erzeugung und Konsum geändert
    haben.
    
    Dabei liest der Client den Feed  ab einschliesslich T1 bis zu einem T5
    knapp größer als T4 und merkt sich die ID des Events (Event ID ==
    "Content-ID” header des Event-Parts im Feed).
    
    Für alle diese items holt der Client per GET den aktuellen Stand ab (bei
    Lösch-Events wird das item gelöscht).
    
    Danach liest der Client den Feed weiter ab der gemerkten Content-ID.
    (Content-Ids sind per existierendem RFC eindeutig in einem
    Multipart-Dokument). Ab jetzt können auch PATCH-Updates angewendet werden.
    
    Die Granularität der Timestamps ist dabei egal und es muss eigentlich auch
    nichts mehr spezifiziert werden (höchstens, dass innerhalb des Feeds die
    Events jedes gegebenen items im Falle von PATCH-Sematik je item zeitlich
    sortiert sein müssen)"


If consumers that process snapshot and feed in parallel encounter events from
the snapshot and the feed that apply to the same target resource and have the
same timestamp they MUST apply the event from the feed, not the snapshot
entity.



Security Considerations
=======================

TBD

--- back

Acknowledgements
================

Thanks to TBD


Frequently Asked Questions
==========================

TBD

Open Issues
===========

TBD