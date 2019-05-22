- 安装 PostgreSQL

    ```bash
    docker run --name learn-sql -v /Users/slackbuffer/db-data/psql-data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=slackbuffer -e POSTGRES_USER=hofungkoeng -e POSTGRES_DB=psql -d postgres

    docker exec -it learn-sql bash
    psql -U hofungkoeng -d psql

    docker exec -it learn-sql psql -U hofungkoeng -d psql

    docker start learn-sql
    show server_encoding;
    SELECT version();
    ```

    - > https://hub.docker.com/_/postgres
- `CREATE DATABASE shop;`
- `\l` - lists databases
- `\c shop` - 从 `psql` 切到 `shop`
    - `docker exec -it learn-sql psql -U hofungkoeng -d shop`
- `\q` - 退出 postgres
- `\d` - checks all tables in the database
- `\d+ table_name` - checks the details of a table