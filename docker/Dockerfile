# Stage 1: Builder
FROM golang:alpine AS builder

# Install tools needed for downloading
RUN apk add --no-cache curl tar

# Download Speedtest
RUN export ARCH=$(uname -m) && \
    curl -fSL "https://install.speedtest.net/app/cli/ookla-speedtest-1.2.0-linux-${ARCH}.tgz" -o speedtest.tgz && \
    tar -xzf speedtest.tgz && \
    mv speedtest /usr/bin/speedtest

# Build grpcurl
ENV CGO_ENABLED=0
RUN go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest

# Stage 2: Final Image
FROM alpine:latest

# Install runtime dependencies
RUN apk add --no-cache bash curl parallel gcompat

# Copy binaries from the builder stage
COPY --from=builder /usr/bin/speedtest /usr/bin/speedtest
COPY --from=builder /go/bin/grpcurl /usr/bin/grpcurl

# Download the client script
RUN curl -fSL https://raw.githubusercontent.com/Tysonpower/starlinkstatus/main/starlinkstatus_client.sh -o /usr/bin/starlinkstatus_client.sh && \
    chmod +x /usr/bin/starlinkstatus_client.sh

# Accept Speedtest license (creates /root/.config/ookla/speedtest-cli.json)
RUN speedtest --accept-license --accept-gdpr

# Entrypoint
ENTRYPOINT ["/bin/bash", "/usr/bin/starlinkstatus_client.sh", "-k", "$APIKEY", "-s", "-d", "-i", "$INTERVAL"]
