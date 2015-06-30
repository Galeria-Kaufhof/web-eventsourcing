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
    organization: GALERIA Kaufhof GmbH
    email: manuel.kiessling@kaufhof.de
    uri: http://www.galeria-kaufhof.de
 -
    ins: J. Algermissen
    name: Jan Algermissen
    organization: 
    email: algermissen@acm.org
    uri: http://www.jalg.net
    
normative:
  RFC2119:
  RFC5005:
  RFC4229:
  RFC7231:
  
informative:
  RFC5789:
  ResourceSync:
    target: http://www.openarchives.org/rs
    title: ResourceSync Framework Specification
    author:
      - organization: NISO
  WebPackaging:
    target: http://www.w3.org/TR/web-packaging/
    title: Packaging on the Web
    author:
      - ins: Jeni Tennison
      - organization: W3C
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


Notational Conventions
----------------------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in [RFC2119].


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
    Content-Type: multipart/package; boundary="_--------1435019846753"
    Content-Length: 3159122
    Connection: keep-alive
    
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /products/16325906:001:99999
    ETag: W/"12345678"
    Content-Length: 93
    
    {"id":"16325906:001:99999","assets":[{"id":"930770","role":"spot"},
    {"id":"9424","role":"1"}]}
    --_--------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16326538:001:99999
    ETag: W/"85392992223"
    Content-Length: 93
    
    {"id":"16326538:001:99999","assets":[{"id":"931862","role":"spot"},
    {"id":"9425","role":"1"}]}
    --_--------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16326590:001:99999
    ETag: W/"5524442218"
    Content-Length: 93
    
    {"id":"16326590:001:99999","assets":[{"id":"914839","role":"spot"},
    {"id":"9408","role":"1"}]}
    --_--------1435019846753
    Content-Disposition: inline
    Content-Type: application/vnd.acme.product+json
    Content-Location: /product/16327887:001:99999
    ETag: "7782882823"
    Content-Length: 193
    
    {"id":"16327887:001:99999","assets":[{"id":"9517","role":"2"},
    {"id":"919130","role":"spot"},{"id":"9518","role":"1"},{"id":"9514",
    "role":"5"},{"id":"9515","role":"3"},{"id":"9516","role":"4"}]}
    --_--------1435019846753--
    

Change Event Feeds
==================

A change event feed, or simply event feed, informs clients about changes
to the items they already know, or about the addition of new items that
were not previously known.

A feed is a collection of 1 or more multipart documents that together
form one logical feed. Each part of the multipart document describes one
event (called `event entry` from now on).


The Event Target Link Relation
------------------------------

Each event entry in the feed MUST reference an existing resource on the
server and describe the nature of the event that happened regarding that
resource.

This referenced resource is the `event target`.

Event targets MUST be referenced via a link relation using the `Link` HTTP
header, with a URI pointing to the targeted resource, and the `rel`
attribute of the relation set to `"about"`.


The Event Entry Link Relation
-----------------------------

Producers MAY provide an additional link relation of type `self` which
points to a resource that identifies the event entry itself.

See "Resuming feed consumption at a given event" below to learn how clients
can use this relation to consume the feed more efficiently.

If a producer provides this link relation, it MUST provide them for all
entries in the feed, and it MUST resolve GET requests to the related
resources as follows:

Link relation is:

    Link: </changes/1234>;rel="self"

Request is:

    GET /changes/1234

Response is:

    HTTP/1.1 302 Found
    Location: /changes/latest#1234

if the event still is located within the subscription document, and

    HTTP/1.1 301 Moved Permanently
    Location: /changes/archive/985/#1234

if by now the event is located on an archived feed page.


The Event-Type HTTP Header
--------------------------

This specification defines the `Event-Type` HTTP header to provide
consumers of change event feeds with information about the nature of
the event regarding the event target. The nature of the event MUST be
declared for each event entry.

The `Event-Type` header takes as its value a key-value pair with the
key indicating the event type family and the value indicating the event
type name from the family.

Event feed publishers SHOULD only use events from a single family in a
given feed.


The Event-Type family http-equiv
--------------------------------

This specification defines the event type family 'http-equiv'. The
allowed value set for this family is the set of unsafe HTTP methods,
such as PUT, PATCH, DELETE, POST.

Consumers of `http-equiv`-typed events MUST process the event in the
same way as they would process an HTTP request to the event target using
the HTTP verb from the http-equiv set defined via the event type header.

For example, the following event feed entry:

    Link: </products/7628827272>;rel="about"
    Event-Type: http-equiv=PUT
    Content-ID: <1234@products.example.org>
      
    {"id":"1234", "name":"foo"}

MUST be treated by the consumer in the same way the consumer would treat
a `PUT` request to the `/products/7628827272` resource with a request
body of `{"id":"1234", "name":"foo"}`.


The Content-ID header
---------------------

Event feed publishers MUST guarantee the order and uniqueness of events
in a feed, i.e., it must be guaranteed that within one feed (which can
spread over many documents) each entry is identified by a unique id
(via the `Content-ID` header), that newer (younger) events are added at the
end of a feed document, and that events, as identified by their Content-ID,
do not move in regards to their relative position to other events.

Example: A feed that consists of 3 entries at t1, and grows by the entry
*48934* at t2, needs to be represented with the following entry order:

    --------------------- time -------------------------------------->
    t1                                 t2
    Feed order:                        Feed order:
    Content-ID: <98346@example.org>    Content-ID: <98346@example.org>
    Content-ID: <34787@example.org>    Content-ID: <34787@example.org>
    Content-ID: <28934@example.org>    Content-ID: <28934@example.org>
                                       Content-ID: <48934@example.org>


The ETag header
---------------

In order to allow for efficient polling, feed publishers SHOULD make the feed
available conditionally via the `If-None-Match` request header, and SHOULD
therefore provide an `ETag` for the feed that changes if the feed content
changes.


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
    Link: </changes/1234>;rel="self"
    Event-Type: http-equiv=PUT
    Content-Type: application/vnd.gkh.product+json
    Last-Modified: Sat, 09 Jan 2014 15:02:23 GMT
    Content-ID: <1234@products.example.org>
    Content-Length: nnn
      
    {"id":"..." , ... }
    --gc0p4Jq0M:2Yt08jU534c0p
    Link: </products/7623288273>;rel="about"
    Link: </changes/567>;rel="self"
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
    Link: </changes/899919>;rel="self"
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

We treat event feeds very much like archived feeds described in [RFC5005].

An event feed is a set of event feed documents that can be combined to
accurately reconstruct the entries of a logical event feed.

This is achieved by publishing a single event subscription document and
(potentially) many event archive documents.

An event subscription document is a feed document that always contains
the most recently added entries available in the logical feed.

Event archive documents are feed documents that contain less recent entries
in the feed. The set of entries contained in an archive document
published at a particular URI SHOULD NOT change over time.  Likewise,
the URI for a particular archive document SHOULD NOT change over
time.

The following link relations are used to tie subscription and
archived feeds together:

- "prev-archive" - A URI that refers to the immediately preceding
  event archive document.

- "next-archive" - A URI that refers to the immediately following
  event archive document.

- "current" - A URI that, when dereferenced, returns a feed document
  containing the most recent event entries in the feed.

Subscription documents and archive documents MUST have a "prev-
archive" link relation, unless there are no preceding archives
available.  Archive documents SHOULD also have a "next-archive" link
relation, unless there are no following archives available.

Archive documents SHOULD indicate their associated subscription
documents using the "current" link relation.

Archive documents SHOULD also contain an fh:archive element in their
head sections to indicate that they are archives. fh:archive is an
empty element; this specification does not define any content for it.


Consuming
=========

In order to maintain a consistent state of the sourced entities, it is
necessary that consumers adhere to the following processing rules
for snapshots and feeds.


Initializing via Snapshots and Feeds
------------------------------------

Snapshot generation takes a certain amount of time. The start and end
timestamps are provided in the snapshot index. Consumers need to be
aware that the snapshotted entities may change during the course of
snapshot generation and that snapshots are not constrained to include
any changes that happen during their creation.

In order to reach a consistent state after snapshot processing consumers
SHOULD consume the event feed associated with the snapshot for the
snapshot generation timeframe to update potentially changed entities.

When doing so, consumers SHOULD apply the following rules:

- Start processing the event feed at the timestamp larger or
  equal than the snapshot start timestamp (explicitly including the start
  timestamp is important in order to cover any changes that occurred
  within the granularity of the timestamp).
- Consume the feed until the current timestamp and remember that
  timestamp as the starting point for normal feed consumption
- For each entity changed withing that timeframe replace the
  entity with the current state of the producer. This can be done
  by 
  - Using the latest event content for a given entity that has
    http-equiv=PUT semantics.
  - Obtaining the current state of a given entity from the producer
    using an HTTP GET request to the Content-Location associated with
    the entity if the event has http-equiv=PATCH semantics.
  - If the snapshot contains an ETag for a given entity such GET
    requests can be made conditional on that ETag.

Snapshots should generally only be consumed for initialization
or error recovery scenarios. Once an initial state has been
obtained, consumers should process the associated change event feed
to maintain an up-to-date state of the entities.

The following diagram shows how to initialize synchronization and how
to continue synchronization after initialization:

          t: Point in time
          R: Resource
          E: Event
          P: Process step


             Client                      Server                                
          |                                               +  t0:    
          |  t1: Start consuming ------> Serve snapshot:  |  E1 = PUT R1
          | [P1] snapshot              |                  |  E2 = PUT R2
          |                            | +-------------+  |  E3 = PUT R3 
          |  - Store each R in the     | |             |  |                    
          |    snapshot locally        | |  R1         |  |  t2:
          |  - Store ts                | |  R2         |  |  E4 = PATCH R2
          |                            | |  R3         |  |  E5 = PUT R4    
          |                            | |             |  |                    
          |                            | |  ts te      |  |                    
          |                            | +-------------+  |                    
          |                            |                  |                    
    R1  <-------------------------+ t3 v                  |                    
    R2    |                                               |                    
    R3    |                                               |                    
          |  t4: Start consuming ------> Serve feed:      |                    
          |      feed                                     |                    
          |                              +-------------+  |                    
          |  - Find the oldest           |             |  |                    
          |    event that has            | E5@t2       |  |                    
          |    Last-Modified >= ts,      | E4@t2       |  |                    
          |    in this case this         | E3@t0       |  |  t5: 
          |    is E4                     | E2@t0       |  |  E6 = PATCH R4
    R1  <-+  - Process E4, and all       | E1@t0       |  |  E7 = DELETE R1                  
    R2'   |    events that are younger   |             |  |                    
    R3    |    (in this case, E5)        +-------------+  |                    
    R4    |  - However, if an event is                    |
          |    of type PATCH, do not handle               |
          |    the PATCH itself, but instead              |
          |    retrieve the current state of              |
          |    the targeted resource via GET              |
          |    on the resource URI                        |
          |  - Store Content-ID of the                    |                    
          |    event that was processed                   |                    
          |    last - in this case, E5                    |                    
          |                                               |                    
          |  t6: Start consuming ------> Serve feed:      |                    
          | [P2] feed                                     |                    
          |                              +-------------+  |                    
          |  - Find the event with the   |             |  |                    
          |    Content-ID we stored,     | E7@t5       |  |                    
          |    in this case E5           | E6@t5       |  |                    
    R2' <-+  - Process all events        | E5@t2       |  |                    
    R3    |    that are younger than     | E4@t2       |  |                    
    R4'   |    the found event, in this  | E3@t0       |  |                    
          |    case E6 and E7            | E2@t0       |  |                    
          |  - Store Content-ID of the   | E1@t0       |  |                    
          |    event that was processed  |             |  |                    
          |    last - in this case, E7   +-------------+  |                    
          |  - Repeat at P2; in case of                   |                    
          v    of a local disaster, purge                 v                    
               all local data and start at                                     
               P1                                                              
                                                                               
             Order of events:                                                  
             t0 < ts < t1 < t2 < te < t3 < t4 < t5 < t6                        



Resuming feed consumption at a given event
==========================================

If the client received (and stored) a `self` link relation for the events
it retrieved, it can use this relation to more efficiently resume further
feed consumption (compared to always entering via the subscription document
and working backwards from there).

If the link relation of the event last processed by the client was

    Link: </changes/6>;rel="self"

then the client can request the URI in the relation:

    GET /changes/6

The producer MUST then redirect the consumer to the full document
containing the referenced event. If the feed is currently structured like
this:

    /arch/0  /arch/1  /arch/2  /latest
    +---+    +---+    +---+    +---+  
    |   <-----P  <-----P  <-----P  |  
    |   |    |   |    |   |    |   |
    | 0 |    | 2 |    | 5 |    |   |  
    | 1 |    | 3 |    | 6 |    | 8 |  
    |   |    | 4 |    | 7 |    | 9 |
    |   |    |   |    |   |    |   |
    |  N----->  N----->  N----->   |  
    +---+    +---+    +---+    +---+  

then the consumer will be redirected to `/arch/2#6`, where he will find the
event with content id 6, and can work forward within the multipart document
and via the `next-archive` link relation to consume and process all events
that came after event 6.


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

- We could sacrifice Event-Type extensibility to make the Event-Type
  header more easy to process and just define the value as any
  unsafe HTTP method.

- We should define a specialized multipart subtype to express the
  requirement to include a Content-ID header in the parts. For
  example multipart/events or multipart/http-requests.
  This media type should probably also mandate that the parts
  are sorted in descending time order.
  (The early multipart/package type did something like that IIRC)

- I do not like that the extensibility of the Event-Type header
  does not include a reference mechanism to 'follow your nose'
  (Update: complex event types (e.g. moves) seem to be useful
  to some people, hence we should keep the extensibility) 
