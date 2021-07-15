# fluentd-opendistro

## Pre-requisite. Setup your OVN environment

* Alternative 1: Use [Openshift Installer](https://github.com/openshift/installer) to setup an Openshift cluster with OVNKubernetes
* Alternative 2: [Setup a local KIND cluster](https://github.com/ovn-org/ovn-kubernetes/blob/master/docs/kind.md)
    - When invoking `kind.sh`, use the following argument: `./kind.sh -gm shared`

## Installation/configuration steps

(If you are using the KIND setup, you should use `kubectl` instead of `oc`)

1. Deploy `deployment.yml`

```
oc apply -f deployment.yml
```

2. Optionally, set `flows` as the default namespace (or attach `--namespace=flows`  to the rest of commands).

```
oc config set-context --current --namespace=flows
```

3. Wait for all the pods to be up and running

```
$ oc get pods
NAME                       READY   STATUS    RESTARTS   AGE
fluentd-7986c6b6c4-pv9sd   1/1     Running   0          7m15s
opendistro                 2/2     Running   0          7m15s
```

4. Get the local endpoint of the `fluentd` service:

```
$ kubectl describe service fluentd | grep Endpoints:
Endpoints:                10.130.0.6:2055
```

5. In the `ovnkube-node` DaemonSet, manually set the `OVN_NETFLOW_TARGETS` YAML value to the IP:port from the
   previous step:
   
```
kubectl -n ovn-kubernetes edit ds ovnkube-node
```

6. To use Kibana, enable port forwarding to the opendistro pod:

```
kubectl port-forward --address 0.0.0.0 deployments/opendistro 5601:5601
```

Then you can open the following URL in your pod: `http://<host-ip>:5601/`


## TODO

* there is no k8s enrichment out of the box. I've seen this plugin should do the job: https://github.com/fabric8io/fluent-plugin-kubernetes_metadata_filter
* Provide a shared volume to store OpenDistro data
    - avoid loosing data after restart
    - more realistic performance evaluation

## Extra references
* [NetFlow real-time analysis with Fluentd](https://gist.github.com/narutaro/d569cdd48414e09f044e)
* Change cluster logging management state to unmanaged: https://docs.openshift.com/container-platform/4.1/logging/config/efk-logging-management.html#efk-logging-management
* Configure fluentd https://docs.openshift.com/container-platform/4.1/logging/config/efk-logging-fluentd.html
