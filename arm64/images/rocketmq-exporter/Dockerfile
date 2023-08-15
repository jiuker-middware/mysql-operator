FROM --platform=$TARGETPLATFORM maven:3.6.3-jdk-8-openj9 AS builder

RUN apt-get update && apt-get install -y git && \
    git clone https://github.com/apache/rocketmq-exporter.git

RUN cd rocketmq-exporter && \
    mvn clean package -Dmaven.test.skip=truedocker

FROM --platform=$TARGETPLATFORM openjdk:8-jre-alpine

RUN echo "Asia/Shanghai" > /etc/timezone

# 必须使用 exec 文件：https://github.com/apache/rocketmq-exporter/issues/115
COPY --from=builder /rocketmq-exporter/target/rocketmq-exporter-0.0.2-SNAPSHOT-exec.jar /rocketmq-exporter.jar

EXPOSE 5557

ENTRYPOINT ["java","-jar","rocketmq-exporter.jar"]