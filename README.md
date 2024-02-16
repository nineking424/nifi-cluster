# nifi-cluster
```yaml
version: "3"
services:
  # configuration manager for NiFi
  zookeeper:
    hostname: zookeeper
    container_name: zookeeper
    image: 'bitnami/zookeeper:3.8.1'
    restart: on-failure
    environment:
      ALLOW_ANONYMOUS_LOGIN: yes
    volumes:
      - zookeeper-data:/bitnami/zookeeper
    networks:
      - nifi-net
  # version control for nifi flows
  nifi-registry:
    hostname: nifi-registry
    container_name: nifi-registry
    image: 'apache/nifi-registry:1.23.1'
    restart: on-failure
    ports:
      - 18080:18080
    environment:
      LOG_LEVEL: INFO
      NIFI_REGISTRY_WEB_HTTP_PORT: 18080
      NIFI_REGISTRY_DB_DIR: /opt/nifi-registry/nifi-registry-current/database
      NIFI_REGISTRY_FLOW_PROVIDER: file
      NIFI_REGISTRY_FLOW_STORAGE_DIR: /opt/nifi-registry/nifi-registry-current/flow_storage
    volumes:
      - nifi-registry-data:/opt/nifi-registry/nifi-registry-current
    networks:
      - nifi-net
  nifi-01:
    hostname: nifi-01
    container_name: nifi-01
    depends_on:
      - zookeeper
    image: 'apache/nifi:1.23.1'
    restart: on-failure
    ports:
      - 8091:8080
    environment:
      NIFI_WEB_HTTP_PORT: 8080
      NIFI_CLUSTER_IS_NODE: true
      NIFI_CLUSTER_NODE_PROTOCOL_PORT: 8082
      NIFI_ZK_CONNECT_STRING: zookeeper:2181
      NIFI_ELECTION_MAX_WAIT: '30 sec'
      NIFI_SENSITIVE_PROPS_KEY: '12345678901234567890A'
      TZ: Asia/Seoul
    volumes:
      - nifi-01-data:/opt/nifi/nifi-current
    networks:
      - nifi-net
  nifi-02:
    hostname: nifi-02
    container_name: nifi-02
    depends_on:
      - zookeeper
    image: 'apache/nifi:1.23.1'
    restart: on-failure
    ports:
      - 8092:8080
    environment:
      NIFI_WEB_HTTP_PORT: 8080
      NIFI_CLUSTER_IS_NODE: true
      NIFI_CLUSTER_NODE_PROTOCOL_PORT: 8082
      NIFI_ZK_CONNECT_STRING: zookeeper:2181
      NIFI_ELECTION_MAX_WAIT: '30 sec'
      NIFI_SENSITIVE_PROPS_KEY: '12345678901234567890A'
      TZ: Asia/Seoul
    volumes:
      - nifi-02-data:/opt/nifi/nifi-current
    networks:
      - nifi-net
  nifi-03:
    hostname: nifi-03
    container_name: nifi-03
    depends_on:
      - zookeeper
    image: 'apache/nifi:1.23.1'
    restart: on-failure
    ports:
      - 8093:8080
    environment:
      NIFI_WEB_HTTP_PORT: 8080
      NIFI_CLUSTER_IS_NODE: true
      NIFI_CLUSTER_NODE_PROTOCOL_PORT: 8082
      NIFI_ZK_CONNECT_STRING: zookeeper:2181
      NIFI_ELECTION_MAX_WAIT: '30 sec'
      NIFI_SENSITIVE_PROPS_KEY: '12345678901234567890A'
      TZ: Asia/Seoul
    volumes:
      - nifi-03-data:/opt/nifi/nifi-current
    networks:
      - nifi-net
volumes:
  nifi-registry-data: # data volume for nifi-registry
   driver: local
  zookeeper-data: # data volume for zookeeper
   driver: local
  nifi-01-data: # data volume for nifi-01
   driver: local
  nifi-02-data: # data volume for nifi-02
   driver: local
  nifi-03-data: # data volume for nifi-03
   driver: local
networks:
  nifi-net:
   driver: bridge
```
