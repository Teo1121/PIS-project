# PIS-project

## Opis:

Studenti će moći ocijeniti, usporediti i komentirati profesore i asistente. Te informacije će se moći zabilježiti kao javne da svi mogu vidjeti ili privatne da samo admin i student mogu vidjeti. Grupe korisnika su studenti s pohvalama ili žalbama na profesore/asistente, te administracija sveučilišta, tj. Ured za strateško planiranje i osiguravanje kvalitete. Profesorima i asistentima je zabranjen pristup.

## How to start:

1. create docker bridged network

```
docker network create ocjenjivanje
```

2. run mysql with docker

```
docker pull mysql
```

```
docker run --net ocjenjivanje --name ocjenjivanje-mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_ROOT_HOST=% -p 3306:3306 -d mysql:latest
```

3. run jsb with docker

```
docker build -t ocjenjivanje_backend .
```

```
docker run --net ocjenjivanje --name ocjenjivanje-backend -p 8080:8080 -d ocjenjivanje_backend
```

4. run frontend with docker

```
docker build -t ocjenjivanje_frontend --network=host .
```

```
docker run --net ocjenjivanje --name ocjenjivanje-frontend -p 3000:3000 -d ocjenjivanje_frontend
```
