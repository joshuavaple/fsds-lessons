MLFLOW_VERSION=2.3.2

build-before-msb:
	docker build -t before_msb -f mlflow/Dockerfile \
	--build-arg MLFLOW_VERSION==$(MLFLOW_VERSION) \
	mlflow

run-before-msb:
	docker run -p 5000:5000 before_msb

build-and-run-before-msb: build-before-msb run-before-msb

build-after-msb:
	docker build -t after_msb -f mlflow/Dockerfile-multistage-build \
	--build-arg MLFLOW_VERSION==$(MLFLOW_VERSION) \
	mlflow

run-after-msb:
	docker run -p 5000:5000 after_msb

build-and-run-after-msb: build-after-msb run-after-msb