# ClickHouse for d8a's events, sized for a Railway
# service instead of a whole server, with a read-only user for Blazer.
# upgrade: dockerhub clickhouse/clickhouse-server
FROM clickhouse/clickhouse-server:26.3.17.4
# The image's network config listens on :: and 0.0.0.0, and the second fails with
# "address already in use" on every start; ours listens on :: (IPv6 and IPv4).
RUN rm /etc/clickhouse-server/config.d/docker_related_config.xml
COPY config.d/ /etc/clickhouse-server/config.d/
COPY users.d/ /etc/clickhouse-server/users.d/
