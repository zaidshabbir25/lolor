# Installing and Configuring lolor

### Using the pgEdge CLI

You can use the CLI's Update Manager (`um`) module to install the lolor extension. After installing pgEdge Distributed Postgres, navigate into the `pgedge` directory and add lolor to your installation with the command:
```
./pgedge um install lolor
```

### Using Enterprise Native Packages

lolor is also available through enterprise native packages for both RPM-based (RHEL, Rocky Linux, AlmaLinux, Oracle Enterprise Linux) and Debian-based (Debian, Ubuntu) distributions. For installation instructions and package availability, visit the [pgEdge Enterprise Postgres](https://www.pgedge.com/products/what-is-pgedge-enterprise-postgres) documentation.

### Building from Source

You can also compile and install the extension from the [source code](https://github.com/pgEdge/lolor), with the same guidelines as any other Postgres extension constructed using PGXS. Make sure that your PATH environment variable includes the directory where `pg_config` (under your PostgreSQL installation) is located.
```
export PATH=/usr/pgsql-18/bin:$PATH

# compile
make USE_PGXS=1

# install (may require sudo)
make USE_PGXS=1 install

# compile
make USE_PGXS=1
make USE_PGXS=1 install
```

Once lolor is installed, connect to your Postgres database and create the extension with the command:

```
CREATE EXTENSION lolor;
```

## Configuring lolor

You must set the `lolor.node` parameter on each node in your replication cluster before using the extension. The value can be from 1 to 2^28; the value is used to help in generation of new large object OID.

```
lolor.node = 1
```

You can also change the `search_path` to pick large object related tables from the `lolor` schema:

```
SET search_path=lolor,"$user",public,pg_catalog
```

lolor renames any existing methods in `pg_catalog.lo_*` to `pg_catalog.lo_*_orig`, and new versions of these methods are introduced.  If you remove the extension, the renamed `pg_catalog.lo_*_orig` functions are restored to their initial names.

While using `pgedge` replication with large objects, you must have the `pg_largeobject` and `pg_largeobject_metadata` tables in your replication set; use 
the following commands to add the tables:

```
./pgedge spock repset-add-table spock_replication_set 'lolor.pg_largeobject' lolor_db
./pgedge spock repset-add-table spock_replication_set 'lolor.pg_largeobject_metadata' lolor_db
```
