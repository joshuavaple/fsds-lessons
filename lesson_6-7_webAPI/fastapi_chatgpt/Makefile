# modified commands to run the app by makefile:
# 1. Specify the shell to be bash, because makefile uses /bin/sh by default
# 2. Load the environment variables from the .env file
# 3. Run the app using uvicorn on the same "line" as the environment variables with &&
# otherwise, the 2 lines will run in different shells and the environment variables will not be available

SHELL := /bin/bash

run-local-dev:
	set -a && source .env && set +a && \
	uvicorn main:app --host 0.0.0.0 --port 8081