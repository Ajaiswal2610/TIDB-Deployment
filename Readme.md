# TiDB High Availability Cluster Deployment

This repository contains Docker Compose configuration for deploying a highly available TiDB cluster with monitoring capabilities.

## Architecture

The deployment includes:
- 3 PD (Placement Driver) nodes for cluster management
- 3 TiKV nodes for storage
- 3 TiDB nodes for SQL processing
- 1 TiFlash node for analytical processing
- TiProxy for connection management
- Monitoring stack (Prometheus, Grafana, Node Exporter)

## Prerequisites

- Docker Engine (version 19.03.0+)
- Docker Compose (version 1.29.0+)
- Minimum 16GB RAM
- At least 50GB free disk space
- Linux/MacOS system (Windows users should use WSL2)

## Quick Start

1. Clone this repository:
```bash
git clone <repository-url>
cd <repository-name>
```

2. Create necessary directories:
```bash
mkdir -p data logs tiflash_data
```

3. Start the cluster:
```bash
docker-compose up -d
```

4. Verify the deployment:
```bash
docker-compose ps
```

## Component Access

### Database Access
- TiDB Server: `localhost:4000`
  - Default user: `root`
  - Default password: (empty)
- TiProxy: `localhost:6000`

### Monitoring & Management
- Grafana: `http://localhost:3000`
  - Default user: `admin`
  - Default password: `admin`
- Prometheus: `http://localhost:9090`
- TiProxy Dashboard: `http://localhost:3080`
- PD Dashboard: `http://localhost:2379/dashboard`

### Component Ports
- PD: 2379, 2380
- TiKV: 20160
- TiDB: 4000 (MySQL), 10080 (status)
- TiFlash: 
  - 3930 (Service)
  - 20170 (Proxy)
  - 20181 (Status)
  - 9000 (MPP)
  - 8123 (HTTP)
- TiProxy: 6000 (SQL), 3080 (HTTP)
- Monitoring:
  - Prometheus: 9090
  - Grafana: 3000
  - NG Monitoring: 12020

## Connecting to TiDB

### Using MySQL Client
```bash
mysql -h 127.0.0.1 -P 4000 -u root
```

### Using TiProxy (Recommended for HA)
```bash
mysql -h 127.0.0.1 -P 6000 -u root
```

## Monitoring

1. Access Grafana at `http://localhost:3000`
2. Login with default credentials (admin/admin)
3. Browse pre-configured dashboards for:
   - TiDB cluster overview
   - TiDB node performance
   - TiKV node performance
   - TiFlash metrics

## Scaling

The cluster is configured with:
- 3 PD nodes for HA
- 3 TiKV nodes for data replication
- 3 TiDB nodes for SQL processing

To modify the scale:
1. Update the docker-compose.yml file
2. Run `docker-compose up -d` to apply changes

## Data Persistence

Data is persisted in Docker volumes:
- pd_data, pd1_data, pd2_data: PD data
- tikv_data, tikv1_data, tikv2_data: TiKV data
- tiflash_data: TiFlash data

## Stopping the Cluster

```bash
docker-compose down
```

To remove all data:
```bash
docker-compose down -v
```

## Troubleshooting

1. Check component status:
```bash
docker-compose ps
```

2. View logs:
```bash
docker-compose logs <service-name>
```

3. Common issues:
   - Insufficient memory: Ensure at least 16GB RAM
   - Port conflicts: Check if ports are already in use
   - Volume permissions: Ensure proper read/write permissions

## Security Notes

- Default configuration uses HTTP. For production, configure TLS
- Default passwords should be changed
- Review firewall rules and expose only necessary ports

## Production Considerations

1. Configure TLS for all components
2. Adjust resource limits based on workload
3. Implement proper backup strategy
4. Monitor disk space and system resources
5. Configure alerting rules
6. Use separate physical/virtual machines for better isolation

## Additional Resources

- [TiDB Documentation](https://docs.pingcap.com/tidb/stable)
- [TiDB Architecture](https://docs.pingcap.com/tidb/stable/architecture)
- [PingCAP Blog](https://pingcap.com/blog)