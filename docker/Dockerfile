FROM python:3.7.14-slim

ARG AIRFLOW_USER_HOME=/usr/local/airflow
ENV AIRFLOW_HOME=${AIRFLOW_USER_HOME}

RUN set -ex \
    && apt-get update -yqq \
    && apt-get upgrade -yqq \
    && apt-get install -yqq --no-install-recommends \
        build-essential \
        apt-utils \
        default-libmysqlclient-dev \
        procps \
        netcat \
        tini \
    && useradd -u 5000 -ms /bin/bash -d ${AIRFLOW_USER_HOME} airflow \
    && pip install -U pip

RUN chown -R airflow: ${AIRFLOW_USER_HOME}

EXPOSE 8081

USER airflow
WORKDIR ${AIRFLOW_USER_HOME}

ENV PATH="${PATH}:/home/airflow/.local/bin/airflow:/usr/local/airflow/.local/bin"

COPY airflow_prometheus_exporter-1.0.8.tar.gz .

RUN pip3 install --user apache-airflow[mysql]==1.10.14 \
	airflow_prometheus_exporter-1.0.8.tar.gz \
    SQLAlchemy==1.3.23 \
    Flask-SQLAlchemy==2.4.4
	
ENV DATABASE_DB="airflow"
ENV DATABASE_HOST="dev-cloud-sql-proxy.dev-airflow.svc.cluster.local"
ENV DATABASE_PORT="3306"
ENV DATABASE_USER="airflow"

ENTRYPOINT ["/usr/bin/tini", "-s", "--"]
