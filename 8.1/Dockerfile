FROM webdevops/php-apache-dev:8.1

COPY common/conf/bin/debug /usr/local/bin
RUN chmod 755 /usr/local/bin/debug

RUN echo 'cd /app' >> /root/.bashrc
RUN echo 'su application' >> /root/.bashrc