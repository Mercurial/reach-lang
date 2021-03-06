IMAGE="reachsh/algorand-devnet"

.PHONY: build
build:
	if ! [ -d algorand_network ] ; then \
		echo 'Please run `make generate-network` first' ; \
		exit 1 ; \
	fi
	docker build --build-arg REACH_GIT_HASH="$$(../git-hash.sh)" --tag=$(IMAGE):latest .
	TAG_ONLY=1 ../../scripts/docker-push.sh $(IMAGE)

.PHONY: push
push:
	../../scripts/docker-push.sh $(IMAGE)

.PHONY: run
run: build
	docker-compose run --service-ports algorand-devnet

.PHONY: run-shell
run-shell: build
	docker run --name algodev --rm -it -p 4180:4180 -p 8980:8980 -p 9392:9392 --entrypoint /bin/bash $(IMAGE):latest

.PHONY: status
status:
	@curl --verbose -s \
		-H "X-Algo-API-Token: $$(cat algorand_data/algod.token)" \
		-d "int 1" \
		localhost:4180/v2/teal/compile

.PHONY: index-status
index-status:
	@curl --verbose -s \
		-H "X-Indexer-API-Token: reach-devnet" \
		localhost:8980/v2/transactions?min-round=0

# Intentionally not specifying deps for this because we don't
# want to get into loops.
.PHONY: generate-network
generate-network:
	docker run \
		--volume $$(pwd):/cwd \
		--entrypoint /bin/bash \
		$(IMAGE):latest \
		/cwd/generate_algorand_network.sh

.PHONY: clean-network
clean-network:
	rm -rf algorand_network
