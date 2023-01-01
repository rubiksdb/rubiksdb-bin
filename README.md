- what is rubiks db

  Rubiksdb is a key/value distributed database.  All key/value pairs are stored in B+ tree.
  Every B+ tree page is multi-way replicated with paxos.

  The key is a tuple of <table, binary>, value is a tuple of <present, seqnum, binary>.

  Simple RPC APIs are exposed by the service:

  - `GET`        get up to 8 key/value pairs
  - `CONFIRM`    confirm up to 8 key/value pairs if the are up to date
  - `COMMIT`     commit up to 8 key/value pairs in transaction
  - `ITERATE`    iterate over a table

- local setup

  - `tar xf rubiks-bin.tgz`
  - `source local/hack_aliases`
  - `local/start-rubiks.sh`

- java driver: https://github.com/rubiksdb/rubiksdb-driver-java

- golang driver: https://github.com/rubiksdb/rubiksdb-golang-driver
