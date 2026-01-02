# Lab 2: Logical Clocks and Replication Consistency

**Student:** Mukhammedali Sabitov IT-2305

**Course:** Distributed Computing 

## Overview
This project implements a distributed Key-Value Store running on 3 nodes (A, B, C). It ensures consistency using **Lamport Logical Clocks** and a **Last-Writer-Wins (LWW)** conflict resolution strategy.

## Files
- `node.py` — Server implementation (HTTP, JSON, Lamport Clock logic, Replication).
- `client.py` — CLI tool to interact with the nodes (PUT, GET, STATUS).
- `REPORT.pdf` — Report containing screenshots and analysis of the scenarios.

## Dependencies
- Python 3.x (standard library only)

## How to Run

### 1. Start the Nodes
Replace `<IP-A>`, `<IP-B>`, and `<IP-C>` with actual addresses (or use `localhost` for local testing).

Node A (port 8000):
```bash
python3 node.py --id A --port 8000 --peers http://<IP-B>:8001,http://<IP-C>:8002
```

Node B (port 8001):
```bash
python3 node.py --id B --port 8001 --peers http://<IP-A>:8000,http://<IP-C>:8002
```

Node C (port 8002):
```bash
python3 node.py --id C --port 8002 --peers http://<IP-A>:8000,http://<IP-B>:8001
```

### 2. Client Operations
PUT:
```bash
python3 client.py --node http://localhost:8000 put key value
```

GET:
```bash
python3 client.py --node http://localhost:8000 get key
```

STATUS:
```bash
python3 client.py --node http://localhost:8000 status
```

## Experiments (Scenarios)
- **Scenario A: Delay/Reorder**  
  Node A has an artificial 3s delay when replicating to Node C. Test: send a PUT to A; B updates immediately, C updates after ~3s.

- **Scenario B: Concurrent Writes**  
  Simultaneous PUT x=1 to A and PUT x=2 to B. Result: system converges to the value with higher Lamport timestamp (LWW).

- **Scenario C: Temporary Outage**  
  Stop Node B, update on Node A, then restart B. Result: B accepts updates after restart and consistency is restored.
