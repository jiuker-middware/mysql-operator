FROM golang:1.19.2 as builder

USER root

RUN apt update -y
RUN apt upgrade -y

RUN apt install -y wget gcc unzip
ENV GOPROXY=https://goproxy.cn
ENV GOPATH=/build

WORKDIR /build
RUN wget -O arm64.zip https://github.com/mysql-operator/mysql-operator/archive/refs/heads/arm64.zip
RUN unzip arm64.zip
WORKDIR /build/mysql-operator-arm64
RUN go mod tidy
RUN CGO_ENABLED=0 go build  -o /mysql-operator  cmd/mysql-operator/main.go


FROM ubuntu:20.04
#SHELL ["/busybox/sh", "-c"]

# switch to root for installing software
USER root

COPY --from=builder /mysql-operator /mysql-operator

RUN chmod +x /mysql-operator

ENTRYPOINT ["/mysql-operator"]
CMD ["help"]
