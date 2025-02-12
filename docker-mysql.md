# Guide to creating / running / connecting a containerized mysql database with docker

## Creating

1. Pull the image: This is downloading the image
```
docker pull mysql
```

2. Run the container based of the image. This is a very basic setup.
```
sudo docker run --name name-of-database -e MYSQL_ROOT_PASSWORD=secret -d mysql
```

3. Find out which ports the container is using for us to connect to.
```
sudo docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' name-of-database
```

4. Connecting to database. MyCLI as an example. The host is based off the output from the previous command.
```
mycli -h 172.17.0.2 -u root
```
