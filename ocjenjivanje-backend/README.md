# ocjenjivanje-backend

## Run MySql with docker

```
docker pull mysql
```

```
docker run --net ocjenjivanje --name ocjenjivanje-mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_ROOT_HOST=% -p 3306:3306 -d mysql:latest
```

## Run Java Spring Boot with docker

```
docker build -t ocjenjivanje_backend .
```

```
docker run --net ocjenjivanje --name ocjenjivanje-backend -p 8080:8080 -d ocjenjivanje_backend
```
