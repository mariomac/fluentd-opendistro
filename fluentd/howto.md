Requirements: 8GB

docker build . --tag=quay.io/mmaciasl/fluentd-os:latest

deploy -f deployment.yml

kubectl -n ovn-kubernetes edit ds ovnkube-node
meter en OVN_NETFLOW_TARGETS el host/port que sale del `describe svc fluentd`


* TODO: provide as sidecar/federation for cu rrent Openshift fluentd?

kubectl port-forward --address 0.0.0.0 deployments/opendistro 5601:5601

http://localhost:5601/app/login