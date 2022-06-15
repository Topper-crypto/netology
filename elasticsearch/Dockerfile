FROM centos:7

    RUN yum install wget -y
    RUN yum install -y java-11-openjdk wget curl perl-Digest-SHA

    RUN wget https://зооточка62.рф/docs/elasticsearch-7.17.4-linux-x86_64.tar.gz
    RUN mkdir /usr/elasticsearch && \
        mv elasticsearch-7.17.3-linux-x86_64.tar.gz /usr/elasticsearch && \
        cd /usr/elasticsearch && \
        tar -xzf elasticsearch-7.17.3-linux-x86_64.tar.gz

    COPY elasticsearch.yml /usr/elasticsearch/elasticsearch-7.17.3/config/elasticsearch.yml

    RUN groupadd elasticsearch && \
        useradd elasticsearch -g elasticsearch -p elasticsearch && \
        cd /opt && \
        chown -R elasticsearch:elasticsearch /usr/elasticsearch

    RUN mkdir /var/lib/elasticsearch_data && \
        chown -R elasticsearch:elasticsearch /var/lib/elasticsearch_data && \
        mkdir /var/lib/elasticsearch_logs && \
        chown -R elasticsearch:elasticsearch /var/lib/elasticsearch_logs

    USER elasticsearch

    CMD ["/usr/sbin/init"]
    CMD ["/usr/elasticsearch/elasticsearch-7.17.3/bin/elasticsearch"]
