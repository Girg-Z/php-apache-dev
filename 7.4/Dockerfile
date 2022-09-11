FROM webdevops/php-apache-dev:7.4

COPY common/conf/bin/debug /usr/local/bin
RUN chmod 755 /usr/local/bin/debug

RUN echo 'cd /app' >> /root/.bashrc
RUN echo 'su application' >> /root/.bashrc