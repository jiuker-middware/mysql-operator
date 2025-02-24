ARG ARCH
ARG OS

FROM golang:1.21-alpine AS builder

WORKDIR /
COPY src /src
RUN wget https://github.com/prometheus/mysqld_exporter/archive/refs/tags/v0.13.0.zip -O mysqld_exporter.zip && \
    unzip mysqld_exporter.zip && \
    rm -rf mysqld_exporter.zip

RUN cp ./src/collector/* /mysqld_exporter-0.13.0/collector/ && \
    cp ./src/mysqld_exporter.go /mysqld_exporter-0.13.0/mysqld_exporter.go

RUN cd /mysqld_exporter-0.13.0 && \
    go mod tidy && \
    CGO_ENABLED=0 go build -o /mysqld_exporter mysqld_exporter.go

FROM quay.io/prometheus/busybox-${OS}-${ARCH}:latest

COPY --from=builder /mysqld_exporter /bin/
RUN chown nobody:nobody /bin/mysqld_exporter \
    && chmod 755 /bin/mysqld_exporter

EXPOSE 9104
USER nobody
ENTRYPOINT ["/bin/mysqld_exporter"]
