FROM registry.opensource.zalan.do/stups/planb-cassandra-3.0:latest

# awscli
RUN apt-get update && \
    apt-get install -y wget python3 python3-pip cron rsyslog && \
    rm -rf /var/lib/apt/lists/*
RUN pip3 install awscli


RUN echo '' >> /etc/cassandra/cassandra-env.sh
# jvm memory options
RUN echo 'JVM_OPTS="$JVM_OPTS $(java-dynamic-memory-opts)"' >> /etc/cassandra/cassandra-env.sh
# appdynamics startup options
RUN echo 'JVM_OPTS="$JVM_OPTS $(appdynamics-agent)"' >> /etc/cassandra/cassandra-env.sh


# backup cron script. Files with extensions are not allowed
ENV BACKUP_LOG /var/log/cassandra/backup.log
RUN touch $BACKUP_LOG
COPY backup.sh /etc/cron.daily/ebs-backup
RUN chmod +x /etc/cron.daily/ebs-backup


# Backups are scheduled by cron and that's why require ROOT user
CMD [ ! -z $WITH_BACKUPS ] && env >> /etc/environment && rsyslogd && cron -L15 && { planb-cassandra.sh & tail -F $BACKUP_LOG; } || planb-cassandra.sh
