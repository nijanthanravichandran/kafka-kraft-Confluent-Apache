FROM confluentinc/cp-kafka:7.2.1
RUN sed -i '/KAFKA_ZOOKEEPER_CONNECT/d' /etc/confluent/docker/configure \
&& sed -i 's/cub zk-ready/echo ignore zk-ready/' /etc/confluent/docker/ensure \
&& echo "kafka-storage format --ignore-formatted -t rpGBmdSCQeWvU_7ScmQm3Q -c /etc/kafka/kafka.properties" >> /etc/confluent/docker/ensure