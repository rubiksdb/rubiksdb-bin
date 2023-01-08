- what is rubiks db

  Rubiksdb is a key/value distributed database.  All key/value pairs are stored in B+ tree.
  Every B+ tree page is multi-way replicated with paxos.

  The key is a tuple of <table, binary>, value is a tuple of <present, seqnum, binary>.

- Simple RPC APIs are exposed by the service:

  - `GET`        get up to 8 key/value pairs
  - `CONFIRM`    confirm up to 8 key/value pairs if the are up to date
  - `COMMIT`     commit up to 8 key/value pairs in transaction
  - `ITERATE`    iterate over a table

- local setup

  - `tar xf rubiks-bin.tgz`
  - `source local/hack_aliases`
  - `local/start-rubiks.sh`

- a few facts

  - in rubiks each page takes `32 KiB`, we have `~30 KiB` for key/value pairs.
  - to ensure each page can be splitted, we allow at least 2 key/values pairs in
    a page.  Hence each key/value pair is allowd to be `15 KiB` in size.
  - to ensure not to split a page twice in one transaction, `COMMIT` allows up to
    `15 KiB` bytes for all key/value pairs.
  - the storage transaction allows up to 18 pages and rubiks transaction allows
    up to 8 key/value pairs.

- error codes from RPC call

  - `OK`

    happy RPC

  - `TIMEOUT`

    each RPC is deadline bounded and the service returns timeout when it can't
    serve the request within the deadline.

  - `INVAL`

    when the RPC sends invalid request

  - `STALE`

    for `COMMIT` and `CONFIRM` call. This happens when any key/value pair in
    transaction was updated by someone else.

    this can also be false alarm given that the seqnum is shared by `hash(key) % 4M`.
    the right action is to take is to abort the transaction and retry.

  - `NONEXT`

    for `ITERATE` when it reaches the last key/value pair in the table.

    for `COMMIT` when rubiks is out of storage space. 

  - `EIO`

    happens when there is network problem.

- ORM (object rubiks mapping)

  it is a way to map programing language objects in to key/value pair in database.
  Find example in both java/golang example programs.

- java driver: https://github.com/rubiksdb/rubiksdb-driver-java

- golang driver: https://github.com/rubiksdb/rubiksdb-golang-driver
