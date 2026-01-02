Lab 2: Logical Clocks and Replication Consistency

Student: Mukhammedali Sabitov IT-2305
Course: Distributed Computing
Date: January 2, 2026

Overview

This project implements a distributed Key-Value Store running on 3 nodes (A, B, C). It ensures consistency using Lamport Logical Clocks and a Last-Writer-Wins (LWW) conflict resolution strategy. The system is deployed on AWS EC2 instances and communicates via HTTP with JSON payloads.

Files

node.py: Server implementation (HTTP, JSON, Lamport Clock logic, Replication).

client.py: CLI tool to interact with the nodes (PUT, GET, STATUS).

REPORT.pdf: Report containing screenshots and analysis of the 3 causality scenarios.

Dependencies

Python 3.x (Standard library only)

AWS EC2 instances (Ubuntu 22.04 LTS)

Security Groups configured for ports 8000, 8001, 8002.

How to Run
1. Start the Nodes

Use the Private IP addresses of your instances for internal communication.

Node A (Port 8000):

code
Bash
download
content_copy
expand_less
python3 node.py --id A --port 8000 --peers http://<IP-B>:8001,http://<IP-C>:8002

Node B (Port 8001):

code
Bash
download
content_copy
expand_less
python3 node.py --id B --port 8001 --peers http://<IP-A>:8000,http://<IP-C>:8002

Node C (Port 8002):

code
Bash
download
content_copy
expand_less
python3 node.py --id C --port 8002 --peers http://<IP-A>:8000,http://<IP-B>:8001
2. Client Operations

Run these commands from a separate terminal to interact with the cluster:

PUT data:

code
Bash
download
content_copy
expand_less
python3 client.py --node http://localhost:8000 put key value

GET data:

code
Bash
download
content_copy
expand_less
python3 client.py --node http://localhost:8000 get key

Check Status (Clock & Store):

code
Bash
download
content_copy
expand_less
python3 client.py --node http://localhost:8000 status
Experiments (Scenarios)
Scenario A: Delay/Reorder

The code in node.py includes an artificial 3-second delay when Node A replicates data to Node C.

Test: Send a PUT to Node A.

Result: Node B updates immediately, while Node C updates after ~3s. This demonstrates how the system handles asynchronous message delivery.

Scenario B: Concurrent Writes

Test: Send PUT x 1 to Node A and PUT x 2 to Node B nearly simultaneously.

Result: The system compares Lamport timestamps. The value with the highest timestamp wins (Last-Writer-Wins). If timestamps are tied, the node ID (alphabetical order) acts as a tie-breaker.

Scenario C: Temporary Outage

Test: Stop Node B (Ctrl+C), perform updates on Node A, then restart Node B.

Result: Node B initially misses the update but re-syncs and achieves eventual consistency upon the next successful replication or status check.

Deliverables

Code Repository: Updated node.py with Lamport logic and artificial delays.

Report: Documentation of experiments with console log screenshots proving convergence.
