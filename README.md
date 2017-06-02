[![No Maintenance Intended](http://unmaintained.tech/badge.svg)](http://unmaintained.tech/)

This project is no longer maintained.  If you have the time and resources to
respond to and vet community feedback, please volunteer for a maintainer role by
posting a message on [this issue][volunteer].

[volunteer]: https://github.com/anchor/nagios-plugin-elasticsearch/issues/48


check_elasticsearch
===================

An [ElasticSearch] availability and performance monitoring plugin for 
Nagios.

[ElasticSearch]: http://www.elasticsearch.org/


How it works
------------

This plugin works by submitting API requests to a local or remote 
ElasticSearch server.  ElasticSearch servers will respond to these API 
requests by default.  If yours don't, check that you have not set 
`http.enabled` to `false`.

This plugin may be used to monitor ElasticSearch data and proxy nodes.  
It is important that you run one instance of this plugin for each data 
node in your ElasticSearch cluster.  Certain failure modes that can be 
localised to a particular data node will not be reported by unaffected 
data or proxy nodes.

ElasticSearch defines its [own thresholds][cluster-health] for 'green', 
'yellow', and 'red'.  Most other ElasticSearch monitoring plugins simply 
take this 'health colour' and map it directly to a Nagios check status 
(OK, WARNING, or CRITICAL, respectively).  While that approach will work 
(insofar as it will report a problem that ElasticSearch has detected), 
an operator is going to need more information to narrow a real problem 
down to its root cause.  ('*Why* is my cluster yellow?')

This monitoring plugin requests detailed operational data from 
ElasticSearch and uses that information to derive its own 'health 
colour'.  If our health colour concurs with ElasticSearch's health 
colour, then we can be reasonably sure we know what is wrong with the 
cluster and may report that problem to the operator.

[cluster-health]: http://www.elasticsearch.org/guide/reference/api/admin-cluster-health.html

Installation
------------

`pip install nagios-plugin-elasticsearch` (from
[PyPi](https://pypi.python.org/pypi/nagios-plugin-elasticsearch/1.0.0)).

Usage:
-----
```
check_elasticsearch [options]

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose
  -f FAILURE_DOMAIN, --failure-domain=FAILURE_DOMAIN
                        A comma-separated list of ElasticSearch attributes
                        that make up your cluster's failure domain[0].  This
                        should be the same list of attributes that
                        ElasticSearch's location-aware shard allocator has
                        been configured with.  If this option is supplied,
                        additional checks are carried out to ensure that
                        primary and replica shards are not stored in the same
                        failure domain.
			[0]: http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/modules-cluster.html
  -H HOST, --host=HOST  Hostname or network address to probe.  The
                        ElasticSearch API should be listening here.  Defaults
                        to 'localhost'.
  -m MASTER_NODES, --master-nodes=MASTER_NODES
                        Issue a warning if the number of master-eligible nodes
                        in the cluster drops below this number.  By default,
                        do not monitor the number of nodes in the cluster.
  -p PORT, --port=PORT  TCP port to probe.  The ElasticSearch API should be
                        listening here.  Defaults to 9200.
  -y YELLOW_CRITICAL, --yellow-critical=TRUE
                        Instead of issuing a 'warning' for a yellow cluster
                        state, issue a 'critical' alert. Allows for greater
                        control of alerting for clusters that may be of 
                        greater sensitivity (or fragility). Defaults to False.

```

Requirements
------------

- Python 2.5 (with simplejson), 2.6, or 2.7
- [pynagioscheck][]

[pynagioscheck]: https://github.com/PeerJ/pynagioscheck
