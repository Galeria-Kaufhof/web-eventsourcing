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
  RFC6570:
  I-D.ietf-httpbis-p7-auth:
  
informative:
  RFC5789:
  I-D.snell-http-prefer:
  WADL:
    target: http://www.w3.org/Submission/wadl/
    title: Web Application Description Language
    author:
      - ins: M. Hadley
      - organization: Sun Microsystems
  MICROFORMATS:
    target: http://microformats.org/
    title: Microformats
    author:
      - organization: microformats.org
  apps-discuss:
    target: https://www.ietf.org/mailman/listinfo/apps-discuss
    title: IETF Apps-Discuss Mailing List
    author:
      - organization: IETF

--- abstract

This document describes HTTP based event sourcing

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
interpreted as described in 


A Section
=========

TBD

A subsection
------------

Snapshots
=========

Snapshot Index Syntax Example
-----------------------------


{
  "uris": [
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/0",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/1",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/2",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/3",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/4",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/5",
    "/resourcesync/variant/snapshot/2015-06-23T00:36:45.885Z/6"
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
    Content-Type: application/entity-images+json
    Content-Location: /entities/variant/16325906:001:99999
    Content-Length: 93
    
    {"id":"16325906:001:99999","assets":[{"id":"930770","role":"spot"},{"id":"9424","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/entity-images+json
    Content-Location: /entities/variant/16326538:001:99999
    Content-Length: 93
    
    {"id":"16326538:001:99999","assets":[{"id":"931862","role":"spot"},{"id":"9425","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/entity-images+json
    Content-Location: /entities/variant/16326590:001:99999
    Content-Length: 93
    
    {"id":"16326590:001:99999","assets":[{"id":"914839","role":"spot"},{"id":"9408","role":"1"}]}
    --_-------------1435019846753
    Content-Disposition: inline
    Content-Type: application/entity-images+json
    Content-Location: /entities/variant/16327887:001:99999
    Content-Length: 193
    
    {"id":"16327887:001:99999","assets":[{"id":"9517","role":"2"},{"id":"919130","role":"spot"},{"id":"9518","role":"1"},{"id":"9514","role":"5"},{"id":"9515","role":"3"},{"id":"9516","role":"4"}]}
    --_-------------1435019846753--
    


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
