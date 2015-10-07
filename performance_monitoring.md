# Performance Monitoring

In order to help with distributed systems performance monitoring a new event
will be added to MonDemand.  The ESF for the event is as follows.

```
MonDemand::PerfMsg
{
  # this first field is assigned at the head of the performance trace
  # and will consist of a set of keys separated by ':' characters
  string id;

  # the number of timings set for the rest of the event
  uint16 num;
  # the next three fields should all be set together and keep track of
  # the component (and optionally sub-components) by adding a label for
  # the component as well as a start and stop time as milliseconds since
  # epoch.  All three fields should be set together
  string label0;
  int64  start0;
  int64  end0;
  # ...
  string label20;
  int64  start20;
  int64  end20;
  # etc.
}
```

Each client library should then support the following changes.

1. support the following new values for configuration, ideally via the
   config file
```
   # an ip or list of ips to emit performance events to, when more than
   # one ip is listed the client should round robin the sending of events
   MONDEMAND_PERF_ADDR="<ip>[,<ip>]?"
   # a port or list of ports to emit performance events to.  If multiple ips
   # are given, but only a single port given, then each ip should use that
   # port.  If more than one port is given the total number should match the
   # total number of ips
   MONDEMAND_PERF_PORT="<port>[,<port>]?"
   # The TTL field is an optional field which is only necessary if multicast
   # ips are being used and it is necessary to use a different value than the
   # default of 5.
   #
   # This field should be a numeric value between 1 and 32, or a list of values
   # If multiple ips are given, but only a single ttl given, then each ip
   # should use that ttl.
   #
   # If more than one ttl is given the total number should match the
   # total number of ips
   MONDEMAND_PERF_TTL="<ttl>[,<ttl>]?"
   # the SENDTO field is an optional field only really pertinent if you include
   # several ADDR's in the ADDR field.  If SENDTO is not set, then all ADDR's in
   # the ADDR field should be sent the event, if SENDTO is set to an integer less
   # than the number of ADDR's in the ADDR field, then each event is sent to that
   # number
   MONDEMAND_PERF_SENDTO="<sendto>"
```
   language specific client libraries may also allow for these to be
   specified in a language specific form but libraries which support
   the config files must support the above.

2. a function of the form
```
send_performance_trace (id, label[], start[], end[])
```
   (with the details of the arguments left to the developer) which immediately
   send a performance event with the given information.

