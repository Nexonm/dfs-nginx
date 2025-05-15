# dfs-nginx
The repository for Nginx configuration as a part of Distributed File System project (dfs project).

## Quick Start

This guide will help you to deploy the DFS system.  By the way, it isn't so quick
Check out you have downloaded [docker engine](https://docs.docker.com/desktop/?_gl=1*pifl2d*_gcl_au*MjA4OTcwNTEwMC4xNzQ3Mjk0NDUw*_ga*NDgyNDk0OTU5LjE3NDcyOTQ0NTE.*_ga_XJWPQMJYHQ*czE3NDcyOTQ0NTAkbzEkZzEkdDE3NDcyOTQ0NTYkajU0JGwwJGgw) and [docker compose](https://docs.docker.com/compose/install/) on your machine. These components are essential as the system starts in docker containers only. Additionally, for quicker setup you need to have a [git](https://github.com/git-guides/install-git).

### Setup prerequisite

Before deploying the nginx server, the other system components should be started.

1. Deploy the [dfs-db-backup](https://github.com/ClayMix-the-creator/dfs-db-backup) with following commands:
```bash unwrap:false
git clone https://github.com/ClayMix-the-creator/dfs-db-backup
cd dfs-db-backup
docker-compose -f docker-compose-back.yml --env-file back.env up --build --force-recreate
```
2. Deploy the [dfs-metadata](https://github.com/Nexonm/dfs-metadata) with following commands:
```bash
git clone https://github.com/Nexonm/dfs-metadata
cd dfs-metadata
docker-compose build
docker-compose run --name metadata-1 -d metadata
docker-compose run --name metadata-2 -d metadata
```
Note, that we use `docker-compose run` instead of `up`. This is done in order to set prefered names of docker containers.
The communication between [database](https://github.com/ClayMix-the-creator/dfs-db-backup) and two [metadata](https://github.com/Nexonm/dfs-metadata) servers is node through *docker network*, specifically `dfs_network`. It should be automatically created with  [database](https://github.com/ClayMix-the-creator/dfs-db-backup) deploymet.

### Nginx deployment

Now, check that your containers of  [metadata](https://github.com/Nexonm/dfs-metadata) servers named exactly as the configuration file stayes:
```conf
upstream metadata_backend {
    server metadata-1:8080 weight=1;
    server metadata-2:8080 weight=1;
}
```

The names should be the same. In our setup we use `metadata-1` and `metadata-2`. You cacn check that on next step, after downloading a repository.

Now, deploy the nginx:
```bash
git clone https://github.com/Nexonm/dfs-nginx
cd dfs-nginx
docker-compose up -d
```

Now you can work with nginx at default port `80`!