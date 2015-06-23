# web-eventsourcing
Event sourcing for the Web

This draft defines a Web based protocol for event sourcing.

Getting Started
===============

In order to compile the I-D from the markdown document, you need the lastest version
of [xml2rfc](http://xml2rfc.ietf.org).

Installation is described in http://tools.ietf.org/tools/ providing the following
instructions for a quick setup:

    easy_install pip   (if you do not have pip)
    pip install xml2rfc

You also need [kramdown](https://github.com/cabo/kramdown-rfc2629)

    gem install kramdown-rfc2629

Then you should be able to run


    ./build.sh draft-kiessling-web-eventsourcing-00


to create TXT and HTML versions of the example draft in `draft-kiessling-web-eventsourcing-00.txt` and `draft-kiessling-web-eventsourcing-00.html`

Note: `build.sh <draft>` calls 

    kramdown-rfc2629 <draft>.md > <draft>.xml
    xml2rfc --text <draft>.xml
    xml2rfc --html <draft>.xml

You can cleanup the generated file with

    ./clean.sh



