# Annotation Events

Often with monitoring systems you have statistics which are streaming in, but have certain actions occuring which you want to overlay on top of those.  In order to support this annotation events will be added.  These events have the following ESF.

```
MonDemand::AnnotationMsg
{
  # a unique id should be generated for each annotation, this is rarely seen but acts
  # as a primary key in downstream data stores.  Usually a uuid is best.
  string id;
  
  # a timestamp representing seconds since epoch UTC that the event being annotated
  # occured
  int64 timestamp;
  
  # a short description of the event
  string description;
  
  # any longer details about the event
  string text;
  
  # tags are a high level sorting mechanism.  They are free form but should adhere to [a-zA-Z0-9_-]+
  uint16 tag_num;
  string tag0;
  string tag1;
  # ...
  string tagN; # where N is tag_num - 1
  
  # contexts are extra information which will be stored but may or may not be accessible
  uint16 ctxt_num;
  string ctxt_k0;    # name of contextual metadata
  string ctxt_v0;    # value of contextual metadata
  # repeated for the number of contextual key/value pairs
}
```

Eventually all libraries should support the sending of these events, although the primary interface will most likely be the mondemand-tool which is packaged with the C library.

# Changes to mondemand-tool

Mondemand-tool should support the following command line for annotations

```
Annotations Options:

  -a [id]:[timestamp]:[desc](:[tag1],[tag2]) 
  -A [text]
  
  The [desc] and [tagN] values should not contain a ':' and the [tagN] values should not contain a ','.
  The [text] field is left separate such that longer values with any characters can be used.
```

Contexts are set as with other fields via the '-c' command line switch.

# Changes to the server

An initial implementation for the mondemand-server is a backend which places the events into elasticsearch as json objects in the same format as that used by anthracite (which allows the use of anthracite as a front end as well as grafana).

The json format of anthracite is to place events under the path

> /anthracite/event/[id]

In the case of mondemand we will instead place them under

> /mondemand/event/[id]

The format is
```
{
  "date": "2015-10-13T23:45:00Z",
  "desc": "short description",
  "tags": [ "tag0", "tag1" ],
  "text": "longer description",
  "context": { "context_key0": "context_value0" }
}
```

The mapping from the event fields should be easy to see, the format of the date must be the UTC format.
