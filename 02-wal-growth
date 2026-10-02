# WAL Growth

## Problem

'pg_wal' starts growing much faster than expected and disk usage keeps increasing.

## Symptoms

- 'pg_wal' directory size increases continuously
- disk usage rises quickly
- replication lag may increase
- old WAL files are not removed
- replication slots may retain WAL

## Investigation

Check WAL directory size:

'du -sh $PGDATA/pg_wal'

Check replication slots:

'SELECT
    slot_name,
    slot_type,
    active,
    restart_lsn,
    confirmed_flush_lsn
FROM pg_replication_slots;'

Check how much WAL is retained by slots:

'SELECT
    slot_name,
    pg_size_pretty(
        pg_wal_lsn_diff(pg_current_wal_lsn(), restart_lsn)
    ) AS retained_wal
FROM pg_replication_slots
WHERE restart_lsn IS NOT NULL;'

Check replication status:

'SELECT
    application_name,
    state,
    sent_lsn,
    write_lsn,
    flush_lsn,
    replay_lsn,
    replay_lag
FROM pg_stat_replication;'

Also check:

- inactive replication slots
- broken or disconnected replicas
- archive failures
- high WAL generation workload
- long-running transactions
- backup or replication processes

## What I want to test

- WAL retention caused by a slow replica
- WAL retention caused by an inactive replication slot
- archive failures
- disk growth during replication lag
- recovery after removing the cause

## Validation

After the issue is fixed:

- WAL retention starts decreasing
- replicas catch up
- inactive slots are handled safely
- 'pg_wal' size returns to normal
- disk usage stabilizes

## Notes

Large WAL usage is usually a symptom, not the root cause. The main task is to identify what is preventing PostgreSQL from recycling old WAL files.
