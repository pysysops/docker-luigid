# Luigid on Docker

Run the Luigi Central Scheduler / Web UI in a lightweight long-running container.

Based on the official Python alpine image. This container runs luigid as a
non-root user: app with uid: 2101.

## Configuration

* `luigid` loads its configuration from `/etc/luigi/client.cfg`
* `luigi` loads its configuration from `/etc/luigi/client.cfg` and its
logging configuration from `/etc/luigi/logging.cfg`

Mount a directory containing a `client.cfg` and `logging.cfg` file(s) to
`/etc/luigi` to provide your own configuration(s).

`docker run -v /your/directory:/etc/luigi --name luigi stockport/luigid`

### Defaults

```
# luigi.conf

[scheduler]
record_task_history: True
state-path: /luigi/state/luigi-state.pickle

[task_history]
db_connection: sqlite:////luigi/state/luigi-task-history.db
```

```
# logging.conf

[loggers]
keys=root

[handlers]
keys=console, file

[formatters]
keys=detail

[formatter_detail]
class=logging.Formatter
format=%(asctime)s %(name)-15s %(levelname)-8s %(message)s

[handler_file]
class=handlers.TimedRotatingFileHandler
args=('/luigi/logs/luigi.log', 'D', '7', 0, None, False, True)
formatter=detail

[handler_console]
class=StreamHandler
formatter=detail

[logger_root]
level=INFO
handlers=console, file
```

## Persistent State
Mount a volume at `/luigi/state` for the `luigid` scheduler state to be persisted
between restarts

`docker run -v /your/state/directory:/luigi/state --name luigi axiom/docker-luigi`


## Logs
Mount a volume at `/luigi/logs` for file access to the `luigid` and `luigi` logs.
The logs are also pushed through syslog.

`docker run -v /your/logs/directory:/luigi/logs --name luigi axiom/docker-luigi`
