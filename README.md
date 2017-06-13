exometer_prometheus [![Build Status](https://travis-ci.org/GalaxyGorilla/exometer_fetch.svg)](https://travis-ci.org/GalaxyGorilla/exometer_prometheus)
===================

This reporter is a building block for a Prometheus scrape point based on
exometer metrics.

Build
-----

    $ ./rebar3 compile

Test
----

    $ ./rebar3 eunit

Usage
-----

Add `exometer_prometheus` to your list of dependencies in `rebar.config`:

```
{deps, [
    {exometer_prometheus, ".*", {git, "https://github.com/GalaxyGorilla/exometer_prometheus.git"}}
]}.
```

Then just configure it:

```
{exometer, 
    {reporters, [
        {exometer_report_prometheus, []}
    ]}
}.
```

There is just one single API function:

```
exometer_report_prometheus:fetch().
```

This will simply deliver all subscribed metrics in Prometheus text format (as
Erlang binary). You can deliver these metrics using your own webserver,
usually on port 8081 and path `/metrics`. However, take care in using the
correct content-type `text/plain; version=0.0.4`.

Exometer vs. Prometheus
-----------------------

Despite the naming of metric types Exometer and Prometheus really differ
somewhat on what each metric is capable of. The largest pain point might be
that exometer histograms map on Prometheus summaries while Prometheus
histograms have no real counterpart in exometer. Also exometer counters are not
necessarily monotonely increasing while Prometheus counters rely on that. The
following snippet will give you an overview how those types are mapped to each
other. For precise examples just have a quick look into the tests, the snippet
is generated by those.

```
# HELP some_counter some exometer counter
# TYPE some_counter gauge
some_counter 1

# HELP some_other_counter some other exometer counter with forced type
# TYPE some_other_counter counter
some_other_counter 1

# HELP some_gauge some exometer gauge
# TYPE some_gauge gauge
some_gauge 1

# HELP some_spiral some exometer spiral
# TYPE some_spiral gauge
some_spiral 1

# HELP some_histogram some exometer histogram
# TYPE some_histogram summary
some_histogram{quantile="0.5"} 0
some_histogram{quantile="0.75"} 0
some_histogram{quantile="0.9"} 0
some_histogram{quantile="0.95"} 0
some_histogram{quantile="0.99"} 0
some_histogram{quantile="0.999"} 0
some_histogram_count 3
some_histogram_sum 0

# HELP some_function some exometer function
# TYPE some_function gauge
some_function{datapoint="port_count"} 6
some_function{datapoint="port_limit"} 65536
```

TODO
----

* Add protocol buffer format
* Add support for `httpd`

