pgroonga_debug_vagrant
======================

Vagrantfile to build PostgreSQL, Groonga and PGroonga for debugging.

## Debugging with gdb

For debugging with gdb, please refer pages below:

* [Getting a stack trace of a running PostgreSQL backend on Linux/BSD - PostgreSQL wiki](https://wiki.postgresql.org/wiki/Getting_a_stack_trace_of_a_running_PostgreSQL_backend_on_Linux/BSD)
* [PostgreSQL の構造とソースツリー（３） — Let's Postgres](http://lets.postgresql.jp/documents/technical/sourcetree/3)

We use two terminals, one for running `psql` and one for running `gdb`.

### Run psql

```
vagrant ssh
sudo -u postgres psql test
```

### Run gdb

In another terminal, run `gdb` and attach to the postgresql backend process.

```
vagrant ssh
sudo gdb postgres `ps auxww | grep '[p]ostgres.*idle' | awk '{print $2}'`
```

Set the breakpoint by executing the following command at the gdb prompt.

```
b ExecResult
```

### Run a SQL

Run a SQL at the `psql` prompt.

```
select 1;
```

### Proceed to the breakpoint at gdb

Then input `c` at the `gdb` prompt.

```
(gdb) c
Continuing.

Breakpoint 1, ExecResult (node=0x1e17f90) at nodeResult.c:75
75    econtext = node->ps.ps_ExprContext;
(gdb)
```

## License

MIT
