- 安装 PostgreSQL

    ```bash
    docker run --name learn-sql -v /Users/slackbuffer/db-data/psql-data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=slackbuffer -e POSTGRES_USER=hofungkoeng -e POSTGRES_DB=psql -d postgres

    docker exec -it learn-sql bash
    psql -U hofungkoeng -d psql

    docker exec -it learn-sql psql -U hofungkoeng -d psql
    ```

    - > https://hub.docker.com/_/postgres
