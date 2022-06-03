# ocjenjivanje-frontend

## Run Frontend with docker

```
docker build -t ocjenjivanje_frontend --network=host .
```

```
docker run --net ocjenjivanje --name ocjenjivanje-frontend -p 3000:3000 -d ocjenjivanje_frontend`
```
