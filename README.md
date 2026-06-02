# distributed-algos

Distributed systems algorithms, readable in Rust. Learn consensus, replication, and consistency by reading code.

## What's Inside

| Module | What you get |
|---|---|
| `vector_clock` | Vector clocks, causal ordering, conflict resolution (LWW, merge) |
| `consensus::paxos` | Full Paxos: proposer, acceptor, learner; simulated multi-node runs |
| `consensus::raft` | Raft consensus: leader election, log replication, state machine |
| `consistency` | Strong / Eventual / Causal consistency models; replica merging |
| `quorum` | Quorum reads/writes with R + W > N; fault-tolerance analysis |
| `gossip` | Gossip protocol with push-pull exchange, convergence tracking |
| `hash_ring` | Consistent hashing ring with virtual nodes; key distribution analysis |
| `leader_election` | Bully and Ring leader election algorithms |
| `cap` | CAP theorem simulation: latency matrices, consistency/availability tradeoffs |
| `coordination` | Multi-node fleet with Raft-backed state proposals and vector clocks |

## Quick Start

```toml
[dependencies]
distributed-algos = "0.1.0"
```

### Vector clocks

```rust
use distributed_algos::vector_clock::VectorClock;

let mut vc1 = VectorClock::new();
vc1.increment("node_a");
vc1.increment("node_a");

let mut vc2 = VectorClock::new();
vc2.increment("node_b");

assert!(vc1.is_concurrent_with(&vc2));
```

### Paxos consensus

```rust
use distributed_algos::consensus::paxos::{PaxosNode, run_paxos};

let mut nodes = vec![
    PaxosNode::new("n0".into(), 3),
    PaxosNode::new("n1".into(), 3),
    PaxosNode::new("n2".into(), 3),
];
run_paxos(&mut nodes, 0, "my_value");
assert_eq!(nodes[0].learned_value, Some("my_value".to_string()));
```

### Raft leader election and log replication

```rust
use distributed_algos::consensus::raft::{RaftNode, simulate_election, replicate_entry};

let ids: Vec<String> = (0..3).map(|i| format!("n{}", i)).collect();
let mut nodes: Vec<RaftNode> = ids.iter().map(|id| {
    let peers: Vec<String> = ids.iter().filter(|p| *p != id).cloned().collect();
    RaftNode::new(id.clone(), peers)
}).collect();

simulate_election(&mut nodes, 0);
replicate_entry(&mut nodes, 0, "set x=42");
```

### Quorum reads/writes

```rust
use distributed_algos::quorum::QuorumSystem;

let mut sys = QuorumSystem::new(5);
sys.write("key", "value");
assert_eq!(sys.read("key"), Some("value".to_string()));
```

### Gossip convergence

```rust
use distributed_algos::gossip::GossipNetwork;

let mut net = GossipNetwork::new(20, 3);
net.seed_rumor(0, "breaking_news");
let rounds = net.run_until_convergence(50);
```

### Consistent hashing

```rust
use distributed_algos::hash_ring::HashRing;

let mut ring = HashRing::new(150);
ring.add_node("server_a");
ring.add_node("server_b");
ring.add_node("server_c");

assert_eq!(ring.get_node("user:42"), ring.get_node("user:42"));
```

## License

MIT OR Apache-2.0
