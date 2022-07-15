image = queries-api
account = $(shell aws sts get-caller-identity --query "Account" --output text)
region = us-east-1
stage = dev
include stage-configs/$(stage)

build:
	docker build -t $(image):$(version) .

login:
	aws ecr get-login-password --region $(region) | \
		docker login --username AWS --password-stdin $(account).dkr.ecr.$(region).amazonaws.com

push: login
	docker tag $(image):$(version) $(account).dkr.ecr.$(region).amazonaws.com/$(image):$(version)
	docker push $(account).dkr.ecr.$(region).amazonaws.com/$(image):$(version)

deploy: namespace
	cat deploy.yaml | sed "s/IMAGE_VERSION/$(version)/g;s/ACCOUNT/$(account)/g" | kubectl -n $(namespace) apply -f -

namespace:
	cat namespace.yaml | sed "s/NAMESPACE/$(namespace)/g" | kubectl apply -f -

secret:
	kubectl -n $(namespace) apply -f secret.yaml
