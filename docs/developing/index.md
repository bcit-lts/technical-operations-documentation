<!-- ---
status: new
--- -->

# Getting started with app development

Apps are developed to be deployed as containers on Kubernetes clusters, so by following consistent workflows, apps are easier to update and maintain.

## Requirements

- [Docker](https://docs.docker.com/get-started/)[^1]
- A [GitHub account](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)[^2]
- [`git`](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)[^3]

## Starting a project

New projects typically start with:

1. Creating a repository in [github.com/bcit-lts](https://github.com/bcit-lts)[^4]
1. Crafting a `README.md` that describes the project purpose
1. Adding a `Dockerfile` and a `docker-compose.yml` to the project root:

    === "Dockerfile"

        ``` yaml title="Example Dockerfile"
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

        ``` yaml title="Example docker-compose.yml"
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

Start local development with `docker compose up`.

See the links in the side menu for more details.

[^1]: [https://docs.docker.com/get-started/](https://docs.docker.com/get-started/)
[^2]: [https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github](https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github)
[^3]: [https://git-scm.com/book/en/v2/Getting-Started-Installing-Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
[^4]: [https://github.com/bcit-lts](https://github.com/bcit-lts)
