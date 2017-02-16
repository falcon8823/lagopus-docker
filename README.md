# Lagopus on Docker

Dockerfile for Lagopus Switch

http://www.lagopus.org/

https://hub.docker.com/r/falcon8823/lagopus-docker/

## How to run Lagpous on Docker?

- Allocate hugetlbfs to each container on the host.
  - And mount hugetlbfs to each container with `-v` Docker option.
- Set the `-m` EAL option to avoid allocating entire memory.
  - For sharing hugepages with each DPDK apps.
- If you use a specific PCI device, set the `-w` or `-b` EAL option.
  - `-w`: Whitelist
  - `-b`: Blacklist
- If you use `igb_uio` driver, mount the `/dev/uio*` with `-v` Docker option.
  - And `--priviledged` Docker option is required.

### Example

- Use 2 PCI devices (with `ibg_uio` driver) below
  - 0000:03:00.0
  - 0000:03:00.1

```
docker run -it \
  -v /dev/uio0:/dev/uio0 \
  -v /dev/uio1:/dev/uio1 \
  -v /mnt/huge_c0:/mnt/huge_c0 \
  falcon8823/lagopus-docker \
  lagopus -d -- -c 0xf -n 2 -m 1024 -w 03:00.0 -w 03:00.1
```

## Tips: Multi NUMA node environment

If you use multi NUMA node environment, you suffers from this error.

```
EAL: WARNING: Master core has no memory on local socket!
```

It may be caused by Docker environment.
To avoid this, set the `--cpuset-mems=0` Docker option.
