# Stage 1: Builder (compiles grpcurl)
FROM golang:alpine AS builder
RUN go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest

# Stage 2: Final Image (minimal runtime)
FROM alpine:latest

# Install dependencies
RUN apk add --no-cache bash curl parallel gcompat

# Copy grpcurl binary from the builder stage
COPY --from=builder /go/bin/grpcurl /usr/bin/grpcurl

# Install Ookla Speedtest
RUN curl -L "https://install.speedtest.net/app/cli/ookla-speedtest-1.2.0-linux-$(uname -m).tgz" -o speedtest.tgz \
    && tar -xzf speedtest.tgz \
    && mv speedtest /usr/bin/speedtest \
    && rm speedtest.tgz speedtest.md speedtest.5

# Install starlinkstatus client
RUN curl -sSL https://raw.githubusercontent.com/hucknz/starlinkstatus/main/starlinkstatus_client.sh -o /usr/bin/starlinkstatus_client.sh \
    && chmod +x /usr/bin/starlinkstatus_client.sh

# Accept Speedtest license
RUN speedtest --accept-license --accept-gdpr

# Set default Environment Variables
ENV APIKEY=""
ENV INTERVAL=900

# Entrypoint
ENTRYPOINT ["/bin/bash", "-c", "/usr/bin/starlinkstatus_client.sh -k \"$APIKEY\" -s -d -i \"${INTERVAL}\""]
