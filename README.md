# Prometheus Slurm Exporter

Prometheus collector and exporter for metrics extracted from the [Slurm](https://slurm.schedmd.com/overview.html) resource scheduling system.

## Exported Metrics

### State of the CPUs

* **Allocated**: CPUs which have been allocated to a job.
* **Idle**: CPUs not allocated to a job and thus available for use.
* **Other**: CPUs which are unavailable for use at the moment.
* **Total**: total number of CPUs.

- [Information extracted from the SLURM **sinfo** command](https://slurm.schedmd.com/sinfo.html)
- [Slurm CPU Management User and Administrator Guide](https://slurm.schedmd.com/cpu_management.html)

### State of the Nodes

* **Allocated**: nodes which has been allocated to one or more jobs.
* **Completing**: all jobs associated with these nodes are in the process of being completed.
* **Down**: nodes which are unavailable for use.
* **Drain**: with this metric two different states are accounted for:
  - nodes in ``drained`` state (marked unavailable for use per system administrator request)
  - nodes in ``draining`` state (currently executing jobs but which will not be allocated for new ones).
* **Fail**: these nodes are expected to fail soon and are unavailable for use per system administrator request.
* **Error**: nodes which are currently in an error state and not capable of running any jobs.
* **Idle**: nodes not allocated to any jobs and thus available for use.
* **Maint**: nodes which are currently marked with the __maintenance__ flag.
* **Mixed**: nodes which have some of their CPUs ALLOCATED while others are IDLE.
* **Resv**: these nodes are in an advanced reservation and not generally available.

[Information extracted from the SLURM **sinfo** command](https://slurm.schedmd.com/sinfo.html)

### Status of the Jobs

* **PENDING**: Jobs awaiting for resource allocation.
* **PENDING_DEPENDENCY**: Jobs awaiting because of a unexecuted job dependency.
* **RUNNING**: Jobs currently allocated.
* **SUSPENDED**: Job has an allocation but execution has been suspended and CPUs have been released for other jobs.
* **CANCELLED**: Jobs which were explicitly cancelled by the user or system administrator.
* **COMPLETING**: Jobs which are in the process of being completed.
* **COMPLETED**: Jobs have terminated all processes on all nodes with an exit code of zero.
* **CONFIGURING**: Jobs have been allocated resources, but are waiting for them to become ready for use.
* **FAILED**: Jobs terminated with a non-zero exit code or other failure condition.
* **TIMEOUT**: Jobs terminated upon reaching their time limit.
* **PREEMPTED**: Jobs terminated due to preemption.
* **NODE_FAIL**: Jobs terminated due to failure of one or more allocated nodes.

[Information extracted from the SLURM **squeue** command](https://slurm.schedmd.com/squeue.html)

### Scheduler Information

* **Server Thread count**: The number of current active ``slurmctld`` threads. 
* **Queue size**: The length of the scheduler queue.
* **DBD Agent queue size**: The length of the message queue for _SlurmDBD_.
* **Last cycle**: Time in microseconds for last scheduling cycle.
* **Mean cycle**: Mean of scheduling cycles since last reset.
* **Cycles per minute**: Counter of scheduling executions per minute.
* **(Backfill) Last cycle**: Time in microseconds of last backfilling cycle.
* **(Backfill) Mean cycle**: Mean of backfilling scheduling cycles in microseconds since last reset.
* **(Backfill) Depth mean**: Mean of processed jobs during backfilling scheduling cycles since last reset.
* **(Backfill) Total Backfilled Jobs** (since last slurm start): number of jobs started thanks to backfilling since last Slurm start.
* **(Backfill) Total Backfilled Jobs** (since last stats cycle start): number of jobs started thanks to backfilling since last time stats where reset.
* **(Backfill) Total backfilled heterogeneous Job components**: number of heterogeneous job components started thanks to backfilling since last Slurm start.

[Information extracted from the SLURM **sdiag** command](https://slurm.schedmd.com/sdiag.html)

*DBD Agent queue size*: it is particularly important to keep track of it, since an increasing number of messages
counted with this parameter almost always indicates three issues:
* the _SlurmDBD_ daemon is down; 
* the database is either down or unreachable;
* the status of the Slurm accounting DB may be inconsistent (e.g. ``sreport`` missing data, weird utilization of the cluster, etc.).

## How to build an RPM package from the relases

Consult the [following document](packaging/rpm/README.md) under the ``packaging/rpm`` subdirectory.

## Distribute the exporter as a Snap package

Consult the [following document](packaging/snap/README.md). **NOTE**: this method requires the use of [Snap](https://snapcraft.io), which is built by [Canonical](https://canonical.com).

## How to build the exporter from the sources

### Debian

Install the Prometheus [Go client library](https://github.com/prometheus/client_golang)

    >>> apt install golang-github-prometheus-client-golang-dev

Use the [Makefile](Makefile) to build and test the code.

**Debian Jessie**: in this release, the Prometheus client library package was available only through the backport archives but the Debian maintainers discontinued it, as explained [here](https://lists.debian.org/debian-backports-announce/2018/07/msg00000.html). Now only __Debian Stretch__ is supported with the previous build method.

### CentOS

Under CentOS not all the GOlang dependencies are available as packages.

**GOPATH**: Since ``go`` version _1.13_ it is better to host the modules in a separate directory otherwise this will generate an error message: _$GOPATH/go.mod exists but should not_

In order to use the [Makefile](Makefile) provided with this repository you can proceed as follows:

1. Install the Golang compiler plus GIT and make:
```bash
yum install git golang-bin make
```

2. Clone this repo and change into the source directory:
```bash
git clone https://github.com/vpenso/prometheus-slurm-exporter.git
cd prometheus-slurm-exporter
```

3. Build a module cache to host the necessary Golang dependencies using the [Go modules](https://blog.golang.org/using-go-modules):
```bash
GOPATH=/tmp/go-modules-cache go mod download
go: finding github.com/alecthomas/template v0.0.0-20190718012654-fb15b899a751               
go: finding github.com/alecthomas/units v0.0.0-20190717042225-c3de453c63f4                                
go: finding github.com/beorn7/perks v1.0.1                                                                         
go: finding github.com/cespare/xxhash/v2 v2.1.0          
go: finding github.com/davecgh/go-spew v1.1.1          
go: finding github.com/go-kit/kit v0.9.0                                      
go: finding github.com/go-logfmt/logfmt v0.4.0                                                          
go: finding github.com/go-stack/stack v1.8.0                                                                                   
go: finding github.com/gogo/protobuf v1.1.1                                                                                
go: finding github.com/golang/protobuf v1.3.2                                                                              
go: finding github.com/google/go-cmp v0.3.0                                                                               
go: finding github.com/google/gofuzz v1.0.0                                         
go: finding github.com/json-iterator/go v1.1.7                            
go: finding github.com/julienschmidt/httprouter v1.2.0                                  
go: finding github.com/konsorten/go-windows-terminal-sequences v1.0.1           
go: finding github.com/kr/logfmt v0.0.0-20140226030751-b84e30acd515  
go: finding github.com/matttproud/golang_protobuf_extensions v1.0.1                                     
go: finding github.com/modern-go/concurrent v0.0.0-20180306012644-bacd9c7ef1dd    
go: finding github.com/modern-go/reflect2 v1.0.1                     
go: finding github.com/mwitkow/go-conntrack v0.0.0-20161129095857-cc309e4a2223
go: finding github.com/pkg/errors v0.8.1                                                                                      
go: finding github.com/pmezard/go-difflib v1.0.0                                                                                 
go: finding github.com/prometheus/client_golang v1.2.1                                                    
go: finding github.com/prometheus/client_model v0.0.0-20190812154241-14fe0d1b01d4                    
go: finding github.com/prometheus/common v0.7.0                                                              
go: finding github.com/prometheus/procfs v0.0.5                              
go: finding github.com/sirupsen/logrus v1.4.2                  
go: finding github.com/stretchr/objx v0.1.1                               
go: finding github.com/stretchr/testify v1.3.0 
go: finding golang.org/x/crypto v0.0.0-20190308221718-c2843e01d9a2 
go: finding golang.org/x/net v0.0.0-20190613194153-d28f0bde5980                                        
go: finding golang.org/x/sync v0.0.0-20181221193216-37e7f081c4d4
go: finding golang.org/x/sys v0.0.0-20191010194322-b09406accb47                                                             
go: finding golang.org/x/text v0.3.0                                                                                        
go: finding gopkg.in/alecthomas/kingpin.v2 v2.2.6                                                                        
go: finding gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405                           
go: finding gopkg.in/yaml.v2 v2.2.2
```

4. Build the executable binary:
```bash
go build
go: downloading github.com/prometheus/client_golang v1.2.1
go: downloading github.com/prometheus/common v0.7.0         
go: extracting github.com/prometheus/common v0.7.0      
go: downloading github.com/sirupsen/logrus v1.4.2
go: downloading gopkg.in/alecthomas/kingpin.v2 v2.2.6                                                                             
go: extracting github.com/prometheus/client_golang v1.2.1                                                                      
go: downloading github.com/prometheus/client_model v0.0.0-20190812154241-14fe0d1b01d4
go: downloading github.com/beorn7/perks v1.0.1          
go: downloading github.com/prometheus/procfs v0.0.5                                      
go: downloading github.com/cespare/xxhash/v2 v2.1.0                          
go: downloading github.com/golang/protobuf v1.3.2                         
go: downloading github.com/matttproud/golang_protobuf_extensions v1.0.1
go: extracting github.com/beorn7/perks v1.0.1  
go: extracting gopkg.in/alecthomas/kingpin.v2 v2.2.6
go: downloading github.com/alecthomas/units v0.0.0-20190717042225-c3de453c63f4
go: downloading github.com/alecthomas/template v0.0.0-20190718012654-fb15b899a751
go: extracting github.com/sirupsen/logrus v1.4.2
go: extracting github.com/cespare/xxhash/v2 v2.1.0
go: downloading golang.org/x/sys v0.0.0-20191010194322-b09406accb47
go: extracting github.com/alecthomas/units v0.0.0-20190717042225-c3de453c63f4
go: extracting github.com/prometheus/client_model v0.0.0-20190812154241-14fe0d1b01d4
go: extracting github.com/matttproud/golang_protobuf_extensions v1.0.1
go: extracting github.com/prometheus/procfs v0.0.5
go: extracting github.com/alecthomas/template v0.0.0-20190718012654-fb15b899a751
go: extracting github.com/golang/protobuf v1.3.2
go: extracting golang.org/x/sys v0.0.0-20191010194322-b09406accb47
go: finding github.com/prometheus/client_golang v1.2.1
go: finding github.com/prometheus/common v0.7.0
go: finding github.com/sirupsen/logrus v1.4.2
go: finding gopkg.in/alecthomas/kingpin.v2 v2.2.6
go: finding github.com/beorn7/perks v1.0.1
go: finding github.com/cespare/xxhash/v2 v2.1.0
go: finding github.com/prometheus/client_model v0.0.0-20190812154241-14fe0d1b01d4
go: finding github.com/golang/protobuf v1.3.2
go: finding github.com/alecthomas/template v0.0.0-20190718012654-fb15b899a751
go: finding golang.org/x/sys v0.0.0-20191010194322-b09406accb47
go: finding github.com/alecthomas/units v0.0.0-20190717042225-c3de453c63f4
go: finding github.com/prometheus/procfs v0.0.5
go: finding github.com/matttproud/golang_protobuf_extensions v1.0.1
```

5. Run the test ( **optional** ): if Slurm command line tools (``sinfo``, ``squeue``, etc.) are not available the test will fail!
```bash
GOPATH=/tmp/gopath-for-cache make test
=== RUN   TestCPUsMetrics
--- PASS: TestCPUsMetrics (0.00s)
    cpus_test.go:29: &{alloc:5725 idle:877 other:34 total:6636}
=== RUN   TestCPUssGetMetrics
--- PASS: TestCPUssGetMetrics (0.01s)
    cpus_test.go:33: &{alloc:18956 idle:7852 other:12408 total:39216}
=== RUN   TestNodesMetrics
--- PASS: TestNodesMetrics (0.03s)
    nodes_test.go:29: &{alloc:250 comp:0 down:67 drain:28 err:0 fail:1 idle:319 maint:0 mix:44 resv:0}
=== RUN   TestNodesGetMetrics
--- PASS: TestNodesGetMetrics (0.10s)
    nodes_test.go:33: &{alloc:328 comp:0 down:230 drain:66 err:0 fail:0 idle:53 maint:0 mix:71 resv:0}
=== RUN   TestParseQueueMetrics
--- PASS: TestParseQueueMetrics (0.01s)
    queue_test.go:29: &{pending:4 pending_dep:0 running:28 suspended:1 cancelled:1 completing:2 completed:1 configuring:1 failed:1 timeout:1 preempted:1 node_fail:1}
=== RUN   TestQueueGetMetrics
--- PASS: TestQueueGetMetrics (0.28s)
    queue_test.go:33: &{pending:8280 pending_dep:3 running:7132 suspended:0 cancelled:1 completing:0 completed:180 configuring:0 failed:245 timeout:2 preempted:0 node_fail:0}
=== RUN   TestSchedulerMetrics
--- PASS: TestSchedulerMetrics (0.02s)
    scheduler_test.go:29: &{threads:3 queue_size:0 last_cycle:97209 mean_cycle:74593 cycle_per_minute:63 backfill_last_cycle:1.94289e+06 backfill_mean_cycle:1.96082e+06 backfill_depth_mean:29324}
=== RUN   TestSchedulerGetMetrics
--- PASS: TestSchedulerGetMetrics (0.03s)
    scheduler_test.go:33: &{threads:3 queue_size:0 last_cycle:20982 mean_cycle:32874 cycle_per_minute:23 backfill_last_cycle:991389 backfill_mean_cycle:1.7385e+06 backfill_depth_mean:11320}
PASS
ok      github.com/vpenso/prometheus-slurm-exporter     0.495s
```

## Command line options

The following is the list of the command line options available on this exporter:

```bash
:~$ prometheus-slurm-exporter -h
Usage of ./prometheus-slurm-exporter:
  -listen-address string
    	The address to listen on for HTTP requests. (default ":8080")
  -log.format value
    	Set the log target and format. Example: "logger:syslog?appname=bob&local=7" or "logger:stdout?json=true" (default "logger:stderr")
  -log.level value
    	Only log messages with the given severity or above. Valid levels: [debug, info, warn, error, fatal] (default "info")
```

## Installation

After successfully ran ``make``, you will have a binary called ``prometheus-slurm-exporter`` under the ``bin/`` subdirectory in your local copy of this repository. You can now copy this binary wherever you have installed the Slurm utilities (sinfo,squeue, sdiag) and then put it into execution, either interactively or through a Systemd unit (an example is available [here](lib/systemd/prometheus-slurm-exporter.service)).

## Prometheus Configuration for the SLURM exporter

It is strongly advisable to configure the Prometheus server with the following parameters:

```
scrape_configs:

#
# SLURM resource manager:
# 
  - job_name: 'my_slurm_exporter'

    scrape_interval:  30s

    scrape_timeout:   30s

    static_configs:
      - targets: ['slurm_host.fqdn:8080']
```

* **scrape_interval**: a 30 seconds interval will avoid possible 'overloading' on the SLURM master due to frequent calls of sdiag/squeue/sinfo commands through the exporter.
* **scrape_timeout**: on a busy SLURM master a too short scraping timeout will abort the communication from the Prometheus server toward the exporter, thus generating a ``context_deadline_exceeded`` error.

The previous configuration file can be immediately used with a fresh installation of Promethues. At the same time, we highly recommend to include at least the ``global`` section into the configuration. Official documentation about __configuring Prometheus__ is [available here](https://prometheus.io/docs/prometheus/latest/configuration/configuration/).

**NOTE**: the Prometheus server is using __YAML__ as format for its configuration file, thus **indentation** is really important. Before reloading the Prometheus server it would be better to check the syntax:

```
$~ promtool check-config prometheus.yml

Checking prometheus.yml
  SUCCESS: 1 rule files found
[...]
```

## Grafana Dashboard

A [dashboard](https://grafana.com/dashboards/4323) is available in order to visualize the exported metrics through [Grafana](https://grafana.com).

The following are screenshots of the dashboard:

![Status of the Nodes](images/Node_Status.png)
![Status of the Jobs](images/Job_Status.png)
![SLURM Scheduler Information](images/Scheduler_Info.png)

## Prometheus references

* [GOlang Package Documentation](https://godoc.org/github.com/prometheus/client_golang/prometheus)
* [Metric Types](https://prometheus.io/docs/concepts/metric_types/)
* [Writing Exporters](https://prometheus.io/docs/instrumenting/writing_exporters/)
* [Available Exporters](https://prometheus.io/docs/instrumenting/exporters/)


## License

Copyright 2017 Victor Penso, Matteo Dessalvi

This is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see http://www.gnu.org/licenses/.


