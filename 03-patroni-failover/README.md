# Patroni Failover

## Problem

The current PostgreSQL primary becomes unavailable and Patroni needs to promote a healthy replica.

## Symptoms

- application connections start failing
- current leader is no longer reachable
- Patroni cluster state changes
- one replica is promoted to leader
- HAProxy backend state changes

## Investigation

Check Patroni cluster state:

'patronictl -c /etc/patroni/patroni.yml list'

Check Patroni service logs:

'journalctl -u patroni -n 100'

Check PostgreSQL status:

'pg_isready'

Check cluster members and roles:

- current leader
- replicas
- replication lag
- timeline changes
- node health

Also check:

- etcd availability
- network connectivity
- PostgreSQL logs
- HAProxy backend status

## What I want to test

- stop the primary PostgreSQL/Patroni node
- observe automatic failover
- verify which replica is promoted
- measure failover time
- verify application connectivity
- bring the old primary back
- confirm it rejoins as a replica

## Validation

After failover:

- exactly one node is leader
- replicas follow the new leader
- application traffic reaches the new primary
- old primary does not return as a second leader
- replication is healthy
- cluster state is stable

## Notes

The important part is not only whether failover works, but whether the old primary can return safely without causing split-brain or data divergence.
