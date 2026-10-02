# Replication Lag

## Problem

PostgreSQL replica starts falling behind the primary and WAL begins to accumulate.

## Symptoms

- increasing replication lag
- growing pg_wal usage
- delayed replay on the replica
- possible disk pressure on the primary

## Investigation

Check replication status:

SELECT
    application_name,
    state,
    sent_lsn,
    write_lsn,
    flush_lsn,
    replay_lsn,
    write_lag,
    flush_lag,
    replay_lag
FROM pg_stat_replication;

Check WAL difference:

SELECT pg_size_pretty(
    pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn)
)
FROM pg_stat_replication;

Then check:

- network connectivity
- standby CPU and disk I/O
- long-running queries on the standby
- replication slots
- WAL generation rate
- PostgreSQL logs

## What I want to test

- transport lag vs replay lag
- replica I/O bottlenecks
- long-running workload on the standby
- WAL accumulation
- recovery after removing the bottleneck

## Validation

After the issue is fixed:

- replication lag returns to normal
- WAL backlog decreases
- replica catches up
- disk usage stabilizes

## Notes

The main goal of this lab is to avoid treating every replication lag problem the same way. First determine whether the delay is in transport or replay, then investigate the relevant layer.
