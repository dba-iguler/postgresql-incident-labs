# etcd Connectivity

## Problem

Patroni loses access to etcd and cannot reliably read or update the distributed cluster state.

## Symptoms

- Patroni logs show etcd timeouts
- connection reset or read timeout errors
- leader lock cannot be refreshed
- node may demote itself
- failover may be triggered
- cluster state may become unstable

## Investigation

Check etcd endpoint health:

'etcdctl endpoint health --cluster'

Check etcd member status:

'etcdctl endpoint status --cluster -w table'

Check Patroni logs:

'journalctl -u patroni -n 100'

Check etcd logs:

'journalctl -u etcd -n 100'

Check network connectivity between nodes:

'ping <etcd-node>'

Check ports:

'ss -lntp | grep -E "2379|2380"'

Also check:

- disk latency
- CPU pressure
- memory pressure
- network packet loss
- etcd quorum
- raft communication between members
- certificate/authentication issues if TLS is enabled

## What I want to test

- stop one etcd member
- block etcd traffic temporarily
- introduce network interruption
- observe Patroni behavior
- check whether quorum is still available
- verify leader lock behavior
- restore connectivity and watch the cluster recover

## Validation

After recovery:

- etcd endpoints are healthy
- quorum is available
- Patroni can access the DCS again
- cluster has only one leader
- replicas are following normally
- no unexpected failover remains in progress

## Notes

A Patroni failover can be caused by a database problem, but it can also start at the DCS layer. For etcd incidents, quorum, network latency and disk fsync performance are important checks.
