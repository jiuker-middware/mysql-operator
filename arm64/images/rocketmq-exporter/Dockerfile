FROM --platform=$TARGETPLATFORM maven:3.6.3-jdk-8-openj9 AS builder

RUN apt-get update && apt-get install -y git && \
    git clone https://github.com/apache/rocketmq-exporter.git

# 去掉 rocketmq-exporter/src/main/java/org/apache/rocketmq/exporter/model/BrokerRuntimeStats.java 文件中包含 getTransferredTps 的一行 否则 exporter 采集会空指针，不用维护两个 exporter 镜像。
RUN sed -i '/getTransferredTps/d' rocketmq-exporter/src/main/java/org/apache/rocketmq/exporter/model/BrokerRuntimeStats.java

RUN cd rocketmq-exporter && \
    mvn clean package -Dmaven.test.skip=truedocker

FROM --platform=$TARGETPLATFORM openjdk:8-jre-alpine

RUN echo "Asia/Shanghai" > /etc/timezone

# 必须使用 exec 文件：https://github.com/apache/rocketmq-exporter/issues/115
COPY --from=builder /rocketmq-exporter/target/rocketmq-exporter-0.0.2-SNAPSHOT-exec.jar /rocketmq-exporter.jar

EXPOSE 5557

ENTRYPOINT ["java","-jar","rocketmq-exporter.jar"]