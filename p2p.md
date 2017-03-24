# DOCKER P2P

## DOCKER ZERONET

```bash
docker run -d \
  --name zeronet \
  --restart=always \
  -v /docker/zeronet:/root/data \
  -p 15441:15441 -p 10.37.1.3:43110:43110 \
  nofish/zeronet
```

```bash
docker run -d \
  --name zeronet \
  -v /docker/zeronet:/root/data \
  -p 15441:15441 -p 43110:43110 \
  nofish/zeronet
```
