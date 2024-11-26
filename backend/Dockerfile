## BASE IMAGE
FROM alpine:3.20.3 AS base

## BUILD IMAGE
FROM base AS build
ARG PB_VERSION=0.23.1
RUN apk add --no-cache unzip
ADD https://github.com/pocketbase/pocketbase/releases/download/v${PB_VERSION}/pocketbase_${PB_VERSION}_linux_amd64.zip /tmp/pb.zip
RUN unzip /tmp/pb.zip -d /tmp/pb

## RUNTIME IMAGE
FROM base AS runtime
RUN apk add --no-cache ca-certificates

# Setup non-root user
ARG USER=default
RUN addgroup -S ${USER} && adduser -S -D -h /home/${USER} -G ${USER} ${USER}
WORKDIR /home/${USER}

# Copy PocketBase files
COPY --chown=${USER}:${USER} --from=build /tmp/pb/pocketbase /home/${USER}/pocketbase

# Switch to non-root user
USER ${USER}
RUN mkdir -p pb_data

# Expose port and run PocketBase
EXPOSE 4322
ENTRYPOINT ["./pocketbase", "serve", "--http=0.0.0.0:4322"]