# Vault: Distributed Object Storage Platform

Vault is a fault-tolerant, distributed object storage system designed to mimic production-grade network storage architectures (like Amazon S3 or Ceph) in a local multi-node cluster. It demonstrates multi-node sharding, real-time healing, and cryptographic validation.

## Architecture & Implementation Overview

Vault operates as a local cluster of 6 independent storage nodes orchestrated by a central API gateway. It prioritizes resilience and self-healing over simple CRUD operations.

**What is Real:**
- **Network Sharding:** Files are sliced into cryptographic 4MB chunks and distributed via HTTP to independent storage node processes.
- **Data Persistence:** Nodes write physical chunks to independent local directories using standard file I/O.
- **Checksum Validation:** Every chunk is hashed (SHA-256) on the client and continuously verified by background scrubber daemons.
- **Self-Healing:** A background daemon actively monitors replication thresholds. If a node fails or a chunk is corrupted, Vault automatically pulls a clean replica and restores quorum.
- **Chaos Injection:** Latency and bit-rot injection directly modify the local network timings and physical file bytes to test system resilience in real-time.

**What is Simulated (Mocked):**
- **Physical Isolation:** All 6 "nodes" run on the same physical machine as independent processes on ports `9001-9006`, rather than distinct servers in separate availability zones.
- **Authentication:** Supabase is integrated, but robust RBAC and user multi-tenancy are simplified for demonstration.
- **Production DNS/Routing:** Traffic routes through `localhost` rather than a global load balancer.

## Core Capabilities

- **Atomic Broadcast & Sharding:** Small files broadcast immediately across the cluster. Large files shard into 4MB pieces with configurable 3x or 5x replication.
- **Google Drive-Style VFS:** A virtual file system overlay enables seamless single-file, multi-file, and full-directory uploads.
- **On-the-fly Reassembly:** Folders download as `.ZIP` archives, dynamically reassembled in-memory from dispersed chunks via `JSZip`.

## Getting Started

### Prerequisites
- Node.js 18+

### Initialization
```bash
# Install dependencies
npm run install:backend

# Initialize SQLite metadata database
npm run init-db
```

### Launching the Cluster
Start the 6 storage nodes and the API gateway:
```bash
# This will spawn the 6 nodes on ports 9001-9006 and the gateway on 3000
npm run start:cluster
```

*(Note: Ensure ports `3000`, and `9001-9006` are available).*

### Registering Nodes
Once the cluster is running, register the nodes with the central gateway:
```bash
npm run register-nodes
```

### Accessing the Interface
- **File Manager:** `http://localhost:3000/files.html`
- **Dashboard:** `http://localhost:3000/dashboard.html`
- **Cluster Diagnostics:** `http://localhost:3000/cluster.html`
- **Chaos Studio:** `http://localhost:3000/chaos.html`
