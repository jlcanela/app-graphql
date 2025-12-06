# app-graphql

This project provides a GraphQL API using PostGraphile, backed by a PostgreSQL database.

## Prerequisites

- [Podman](https://podman.io/)
- [podman-compose](https://github.com/containers/podman-compose)
- [Node.js](https://nodejs.org/) and [pnpm](https://pnpm.io/)

## Getting Started

1.  **Start the backend services:**

    Run the following command to start the PostgreSQL database, pgAdmin, and Jaeger tracing services in the background:

    ```bash
    podman-compose up -d
    ```

2.  **Start the GraphQL server:**

    Install the dependencies and run the development server. The server will watch for changes and automatically restart.

    ```bash
    pnpm install
    pnpm run graphql-start
    ```

    The PostGraphile server will be available at the URL output to the console (typically `http://localhost:5678/graphiql`).

## Services

The `podman-compose.yaml` file defines the following services:

-   **PostgreSQL**: The application database.
    -   Port: `5432`
-   **pgAdmin**: A web-based administration tool for PostgreSQL.
    -   URL: [http://localhost:8090](http://localhost:8090)
    -   Login: `admin@admin.com` / `admin`
-   **Jaeger**: A distributed tracing system.
    -   URL: [http://localhost:16686](http://localhost:16686)