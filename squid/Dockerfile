# 使用 Ubuntu 22.04 作为基础镜像
FROM ubuntu:22.04


# 编译并安装 Squid，启用 HTTP/2 支持
RUN apt-get update \
    && apt-get install -y build-essential libssl-dev wget \
    && wget http://www.squid-cache.org/Versions/v5/squid-5.2.tar.gz \
    && tar -xzf squid-5.2.tar.gz \
    && cd squid-5.2 \
    && ./configure  \
    && make \
    && make install \
    && rm -rf squid-5.2 squid-5.2.tar.gz

ENV PATH="/usr/local/squid/sbin:${PATH}"

# 拷贝 Squid 配置文件到容器中
COPY squid1.conf /etc/squid/squid.conf
RUN chmod +x /usr/local/squid/sbin/squid
RUN chmod 777 /usr/local/squid/var/logs/
# 启动 Squid 服务
CMD ["squid", "-N"]


