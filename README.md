## install opa on mac

```sh
brew install opa
```

## build the rego policy

```sh
opa build policy.rego -o bundles/bundle.tar.gz
```

## start the docker containers

```sh
docker-compose up
```

## test the policy.rego by invoking a curl request to the http point

> This will return false because charlie is not allowed to view alice's salary
```sh
curl --location 'http://localhost:8181/v1/data/httpapi/authz' \
--header 'Content-Type: application/json' \
--data '{
    "input": {
        "user": "charlie",
        "path": ["finance", "salary", "alice"],
        "method": "POST"
    }
}'
```
> response: {"decision_id":"69075630-93d2-4cc1-b1b4-2034a572d78a","result":{"allow":false,"subordinates":{"alice":[],"betty":["charlie"],"bob":["alice"],"charlie":[]}}}

> This will be true because alice can view her salary
```sh
curl --location 'http://localhost:8181/v1/data/httpapi/authz' \
--header 'Content-Type: application/json' \
--data '{
    "input": {
        "user": "alice",
        "path": ["finance", "salary", "alice"],
        "method": "POST"
    }
}'
```

> response: {"decision_id":"713f70bd-0c91-4f0c-acd3-706415088cf9","result":{"allow":true,"subordinates":{"alice":[],"betty":["charlie"],"bob":["alice"],"charlie":[]}}}
