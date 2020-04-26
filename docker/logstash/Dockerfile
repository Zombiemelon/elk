FROM docker.elastic.co/logstash/logstash:7.6.2
RUN rm -f /usr/share/logstash/pipeline/logstash.conf
RUN rm -f /usr/share/logstash/config/logstash-sample.conf
RUN rm -f /usr/share/logstash/config/pipelines.yml
COPY ./docker/logstash/logstash.yml /usr/share/logstash/config/logstash.yml
COPY ./docker/logstash/pipeline.conf /usr/share/logstash/pipeline/logstash.conf

CMD ["bin/logstash", "-f", "logstash.yml"]

EXPOSE 5044