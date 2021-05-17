# spanner-emulator-example
https://cloud.google.com/spanner/docs/emulator


### Run the spanner-emulator
```sh
$ docker compose up -d
[+] Running 1/1
 ⠿ Container spanner-emulator  Started                                                                                  4.0s

$ docker compose ps
NAME                SERVICE             STATUS              PORTS
spanner-emulator    spanner-emulator    running             0.0.0.0:9010->9010/tcp, :::9010->9010/tcp, 0.0.0.0:9020->9020/tcp, :::9020->9020/tcp
```
### Using the `gcloud` CLI with emulator
```
$ gcloud config configurations create emulator
$ gcloud config set auth/disable_credentials true
$ gcloud config set project sandbox
$ gcloud config set api_endpoint_overrides/spanner http://0.0.0.0:9020/
```

```sh
$ gcloud config list
[api_endpoint_overrides]
spanner = http://0.0.0.0:9020/
[auth]
disable_credentials = true
[core]
disable_usage_reporting = False
project = sandbox

Your active configuration is: [emulator]

# To switch between the emulator end default configuration, run
$ gcloud config configurations activate [ emulator | default ]

# Create a spanner instance
$ gcloud spanner instances create sandbox-instance --config=emulator-config --description="Develop sandbox" --nodes=1

# Show the project spanner instances
$ gcloud spanner instances list --project=sandbox
NAME              DISPLAY_NAME     CONFIG           NODE_COUNT  STATE
sandbox-instance  Develop sandbox  emulator-config  1           READY

# Create a database
$ gcloud spanner databases create sandbox-db --instance sandbox-instance
Creating database...done.

# Show the instance databases
$ gcloud spanner databases list --instance sandbox-instance
NAME        STATE  VERSION_RETENTION_PERIOD  EARLIEST_VERSION_TIME  KMS_KEY_NAME
sandbox-db  READY
```

### Using `spanner-cli` connect to spanner-emulator
```sh
# install spanner-cli
$ go get -u github.com/cloudspannerecosystem/spanner-cli

$ export SPANNER_EMULATOR_HOST="0.0.0.0:9010"

# connect to spanner-emulator
$ spanner-cli -p sandbox -i sandbox-instance -d sandbox-db
Connected.
spanner> show tables;
Empty set (0.01 sec)

spanner> create table todo(id int64, title string(max)) primary key(id);
Query OK, 0 rows affected (0.00 sec)

spanner> insert into todo(id, title) values(101, "寿司食べたい！");
Query OK, 1 rows affected (0.01 sec)

spanner> select * from todo;
+-----+----------------+
| id  | title          |
+-----+----------------+
| 101 | 寿司食べたい！ |
+-----+----------------+
1 rows in set (380.626us)
```
