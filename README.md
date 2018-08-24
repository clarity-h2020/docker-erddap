# CLARITY ERDDAP

Based on [https://github.com/axiom-data-science/docker-erddap](axiom-data-science/docker-erddap) + additional project specific configuration.

## Starting and stopping

**Create ERDDAP Service**

```bash
cd /docker/300-erddap \
docker-compose up -d --force-recreate --remove-orphans
```

**Delete ERDDAP Service**
```bash
cd /docker/300-erddap \
docker-compose down
```
**Start ERDDAP Service**
```bash
cd /docker/300-erddap \
docker-compose start
```

**Start ERDDAP Service**
```bash
cd /docker/300-erddap
docker-compose stop  
```  

**Check ERDDAP Service Status**
```bash
cd /docker/300-erddap 
docker-compose ps 
docker-compose logs   
``` 

## Data Volumes & Backups

See [docker-compose.yml](https://github.com/clarity-h2020/docker-erddap/blob/clarity/docker-compose.yml) for configuration of *host-mounted* volumes. In general, data and configuration will be persisted in `/docker/300-erddap`. Configuration that is not part of this GitHub Repository is included in "[poor man's backups](https://github.com/clarity-h2020/docker-duplicity)" while data is covered in the general server backups (Veeam Backup & Replication).

## Running ERRDAP CLI Tools 

```bash
$ docker run --rm -it \
  -v $(pwd)/logs:/erddapData/logs \
  axiom/docker-erddap:latest \
  bash -c "cd webapps/erddap/WEB-INF/ && bash GenerateDatasetsXml.sh -verbose"
```

or sh into the running container with

```bash
docker exec -it --user ${tomcat_uid_from} erddap bash
cd webapps/erddap/WEB-INF/
./GenerateDatasetsXml.sh -verbose
```

# ERDDAP on Docker

A feature full Tomcat (SSL over APR, etc.) running [ERDDAP](http://coastwatch.pfeg.noaa.gov/erddap/index.html)

Available versions:

* `axiom/docker-erddap:1.82`
* `axiom/docker-erddap:1.80`
* `axiom/docker-erddap:1.78`
* `axiom/docker-erddap:1.74` - first release based on `unidata/tomcat-docker`
* `axiom/docker-erddap:1.72`
* `axiom/docker-erddap:1.68`
* `axiom/docker-erddap:1.66`
* `axiom/docker-erddap:1.64`

## Quickstart

```bash
$ docker run -d -p 8080:8080 axiom/docker-erddap
```

## Running ERRDAP CLI Tools

**GenerateDatasetsXml**

```bash
$ docker run --rm -it \
  -v $(pwd)/logs:/erddapData/logs \
  axiom/docker-erddap:latest \
  bash -c "cd webapps/erddap/WEB-INF/ && bash GenerateDatasetsXml.sh -verbose"
```


## Configuration

### Tomcat

See [these instructions for configuring Tomcat](https://github.com/unidata/tomcat-docker).


### ERDDAP

1.  Mount your own `content/erddap` directory:

    ```bash
    $ docker run \
        -v /path/to/your/erddap/directory:/usr/local/tomcat/content/erddap \
        ... \
        axiom/docker-erddap
    ```

    Your content directory should contain a [setup.xml](http://coastwatch.pfeg.noaa.gov/erddap/download/setup.html#setup.xml) and [dataset.xml](http://coastwatch.pfeg.noaa.gov/erddap/download/setupDatasetsXml.html) file. It can also include CSS assets that you reference in your custom `setup.xml` file. 

    If you just want to change [setup.xml](http://coastwatch.pfeg.noaa.gov/erddap/download/setup.html#setup.xml) and [dataset.xml](http://coastwatch.pfeg.noaa.gov/erddap/download/setupDatasetsXml.html), you can mount them individually:

    ```bash
    $ docker run \
        -v /path/to/your/setup.xml:/usr/local/tomcat/content/erddap/setup.xml \
        -v /path/to/your/datasets.xml:/usr/local/tomcat/content/erddap/datasets.xml \
        ... \
        axiom/docker-erddap
    ```

    **Any custom setup.xml needs to specify `<bigParentDirectory>/erddapData/</bigParentDirectory>`**


2.  Mount your own `bigParentDirectory`:

    ```bash
    $ docker run \
        -v /path/to/your/erddap/bigParentDirectory:/erddapData \
        ... \
        axiom/docker-erddap
    ```

    This is **highly** recommended, or nothing will persist across container restarts (logs/cache/etc.)
