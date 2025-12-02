---
tags:
  - Docker
  - GitHub
---
<!-- markdownlint-disable code-block-style -->
# Getting started with app development

Apps are developed to run as containers on our Kubernetes clusters. By adopting a standard file structure and following consistent workflows, applications remain easier to update, deploy, and maintain.

## Requirements

Before you begin, ensure you have:

- [Docker](https://docs.docker.com/get-started/)[^1]
- A [GitHub account](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)[^2]
- [`git`](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)[^3]

## Starting a project

A new project typically begins with the following steps:

1. **Create a repository** under [github.com/bcit-lts](https://github.com/bcit-lts)[^4].
1. **Add a clear `README.md`** describing the project purpose, goals, and any required setup.
1. **Add a `Dockerfile` and `docker-compose.yml`** to the project root.

    === "Dockerfile"

        ``` yaml title="Example Dockerfile" linenums="1"
        #-- Build --#
        FROM node:24.6.0-alpine3.22 AS builder

        WORKDIR /app

        COPY package.json ./

        RUN npm install

        COPY . /app

        RUN npm run build

        #-- Clean --#
        FROM nginx:alpine AS cleaner

        WORKDIR /usr/share/nginx/html

        RUN rm -rf ./*

        COPY --from=builder /app/dist ./

        #-- Release --#
        FROM nginxinc/nginx-unprivileged:alpine3.22-perl

        LABEL maintainer=courseproduction@bcit.ca
        LABEL org.opencontainers.image.source="https://github.com/bcit-ltc/course-workload-estimator"

        WORKDIR /usr/share/nginx/html

        COPY conf.d/default.conf /etc/nginx/conf.d/default.conf
        COPY --from=cleaner /usr/share/nginx/html/ ./
        ```

    === "docker-compose.yml"

        ``` yaml title="Example docker-compose.yml" linenums="1"
        name: ${APP_NAME}
        services:
            app:
                build:
                context: .
                target: builder

                command: npm run start

                environment:
                - DEBUG=true

                volumes:
                - ./src:/app/src

                ports:
                - "3000:3000"
        ```

### Local development

To start local development:

``` shell
docker compose up
```

## Next steps

Refer to the links in the side menu for more details about development workflows and conventions.

[^1]: [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
[^2]: [https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)
[^3]: [https://git-scm.com/book/en/v2/Getting-Started-Installing-Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
[^4]: [https://github.com/bcit-lts](https://github.com/bcit-lts)
