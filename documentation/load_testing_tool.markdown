---
title: "PuppetDB 3.2 » Load Testing"
layout: default
canonical: "/puppetdb/latest/load_testing_tool.html"
---

[export]: ./migrate.html

There is a very basic tool for simulating PuppetDB loads included with
the standard PuppetDB distribution. **This tool is currently experimental
and is likely to change in future releases**. Although this tool was
originally intended as a PuppetDB developer tool, documentation is
provided here in the event others would find it useful. It has the
ability to submit catalogs and/or reports at a defined interval, for a
specified number of nodes. It is also able to vary the submitted
catalogs over time to simulate catalog changes (which can cause higher
PuppetDB load). The intent of the tool is to get a rough idea around
how the system (and software) will handle load under realistic
conditions. The tool will use catalogs/reports/facts specified by the user,
which could be exported from a production/running system, to simulate
real-world conditions.

Getting data for the tool
-----

The tool does not yet have the ability to generate its own data.
Before using this tool, you need to get a collection of catalogs, facts
and/or reports to use in the simulation. The easiest way to do this is
via the export tool included with PuppetDB, more information can be
found [here][export]. The export will produce a tar.gz file, which
will contain facts, catalogs and reports. The load testing tool only works on
directories so go ahead and unzip the exported
data. When unzipped/untared, there should be `puppetdb-bak/catalogs`,
`puppetdb-bak/facts` and a `puppetdb-back/reports` directories. One or all of
these directories can be used as input to the load testing tool.

Running the load testing tool
-----

Before running the load testing tool, make sure you have the full path to
your example data. You'll also need a config file (i.e.
`config.ini`) with the host and port information for the PuppetDB
instance you are wanting to load test. The config file format is the
same as the one PuppetDB uses, but you only need two entries:

      [jetty]
      host=<host name here>
      port=<port here>

There is currently no script for running the tool, so you'll need a
command like the one below:

    $ /usr/bin/java -cp /opt/puppetlabs/server/apps/puppetdb/puppetdb.jar clojure.main -m puppetlabs.puppetdb.cli.benchmark --config myconfig.ini --catalogs /tmp/puppetdb-bak/catalogs --runinterval 30 --numhosts 1000 --rand-perc 10

More information on the arguments accepted by the benchmark command
are below.

##### Arguments

- **`--config / -c`** --- Path to the INI file that has the host/port configuration for the PuppetDB instance to be tested
- **`--catalogs / -C`** --- Directory containing catalogs to use for testing (probably from a previous PuppetDB export)
- **`--reports / -R`** --- Directory containing reports to use for testing (probably from a previous PuppetDB export)
- **`--facts / -F`** -- Directory containing facts to use for testing (probably from a previous PuppetDB export)
- **`--archive / -A`** -- Tarball archive obtained via a PuppetDB export. This option is incompatible with the preceding four.
- **`--runinterval / -i`** --- Integer indicating the amount of time in minutes between puppet runs for each simulated node, 30 or 60 would be a typical value for this
- **`--numhosts / -n`** --- Number of separate hosts that the tool should simulate
- **`--rand-perc / -r`** --- What percentage of catalogs submissions should be changed (i.e. simulating normal/typical catalog changes, as in adding a resource, edge or something similar). More changes to catalogs will cause a higher load on PuppetDB, 10 is a pretty typical change percentage.

**NOTE** If --facts, --catalogs, --reports, and --archive are unspecified, the PuppetDB sample data will be used. This data includes catalogs, facts, and reports.
