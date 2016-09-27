## Prerequisites

You need:

* [Virtual Box](https://www.virtualbox.org/) installed
* [Vagrant](https://www.vagrantup.com) installed

## Download packages

Download the following file and put it in the `node/` folder:

* [qdb-server.deb](https://download.quasardb.net/quasardb/nightly/server/qdb-server_2.0.0master-1.deb)

Download the following files and put them in the `testbox/` folder:

* [qdb-utils.deb](https://download.quasardb.net/quasardb/nightly/utils/qdb-utils_2.0.0master-1.deb)
* [qdb-web-bridge.deb](https://download.quasardb.net/quasardb/nightly/web-bridge/qdb-web-bridge_2.0.0master-1.deb)
* [qdb-becnhmark.tar-gz](https://download.quasardb.net/quasardb/nightly/bench/qdb-benchmark-2.0.0-Linux.tar.gz)

## Spawn the cluster

Run the following command:

```shell
export QDB_NODE_COUNT=4
vagrant up
vagrant ssh -c "qdb-benchmark --tests qdb_* --sizes 1M --threads 16 --duration 10 -c qdb://10.0.0.10:2836"
```
