This section provides instructions on deploying the stateful Siddhi App that was discussed in the Introduction section.

### Deploy Siddhi App

Retrieve a prewritten SiddhiProcess YAML, with the earlier discussed `PowerConsumptionSurgeDetection` Siddhi App, using the following command.

`wget https://raw.githubusercontent.com/siddhi-io/katacoda-scenarios/master/siddhi-deployment/distributed-stateful-nats-deployment/power-consume-app.yaml`{{execute}}

Run the following command to view the SiddhiProcess YAML.

`cat power-consume-app.yaml`{{execute}}

Here the given Siddhi App is parametrized to retrieve the `RECEIVER_URL` from environment variables, and configured to be deployed using the docker image `siddhiio/siddhi-runner-ubuntu:5.1.1`. 

As the App is stateful, to persist the periodic states, a persistent volume claim is configured under the `persistentVolume` section, and the relevant persistent configuration of the Siddhi runner is provided under the `runner` section.

Further, since the App is being deployed in a distributed mode, the `messagingSystem` section is configured to use NATS deployed at `nats://nats-siddhi:4222` and using the NATS Streaming cluster `stan-siddhi` to wire the partial Siddhi Apps.

Deploy the Siddhi SiddhiProcess using the below command.

`kubectl apply -f power-consume-app.yaml`{{execute}}

This SiddhiProcess will divide the Siddhi App into two partial Siddhi Apps and deploys them as two Kubernetes deployments, here one (`power-consume-app-0`) consumes the HTTP messages and inserts them into the NATS messaging system, and the other (`power-consume-app-1`) retrieves those events from the NATS messaging system, processes them in a stateful manner using a window, and logs the output. 

### Validate the deployment

Validate the deployment by running the following command.

`kubectl get deploy`{{execute}}

Results similar to the following will be generated, make sure the partial apps `power-consume-app-0` and `power-consume-app-1` are up and running. 

```sh
$ kubectl get deploy
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
power-consume-app-0   1/1     1            1           5m
power-consume-app-1   1/1     1            1           5m
siddhi-operator       1/1     1            1           10m
```

**Note:** The Siddhi Operator parses and validates Siddhi Apps before deploying them. This is done by temporarily deploying a parser with the SiddhiProcess name such as `power-consume-app-parser`, and removing it after parsing.

The status of the `SiddhiProcess` can be viewed using the following commands. The status of the `SiddhiProcess` can be viewed using the following commands. Make sure the `power-consume-app` is in the `Ready` state. The `Ready` state is the indication that the Siddhi app is deployed correctly and ready to consume external requests. The `READY` spec shows the out from all deployments, how many deployments are ready to consume events.

`kubectl get siddhi`{{execute}}

The generate results will be similar to the following. 

```sh
$ kubectl get siddhi

NAME                STATUS    READY     AGE
power-consume-app   Ready     2/2       5m
```

The next section provides information on testing the stateful Siddhi App.
