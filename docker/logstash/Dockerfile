FROM docker.elastic.co/logstash/logstash:7.6.2
COPY ./docker/logstash/logstash.yml /usr/share/logstash/config/logstash.yml
COPY ./docker/logstash/pipeline.conf /usr/share/logstash/pipeline/logstash.conf

CMD ["bin/logstash", "-f", "pipeline.yml"]

EXPOSE 5044