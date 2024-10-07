FROM python:3.9-slim

LABEL maintainer="quandv"
LABEL organization="mlopsvn"

WORKDIR /mlflow/

ARG MLFLOW_VERSION

RUN apt-get update -y
RUN apt-get install -y iputils-ping
RUN pip install --no-cache-dir mlflow==${MLFLOW_VERSION} psycopg2-binary

# for documentation purpose only
# changing it does not affect the actual value
EXPOSE 5000

# Ref: https://github.com/mlflow/mlflow/issues/6274#issuecomment-1196106372
# the port here is the actual port that the mlflow server is running on
CMD mlflow server \
    --backend-store-uri ${BACKEND_STORE_URI} \
    --serve-artifacts \
    --host 0.0.0.0 \
    --port 5000