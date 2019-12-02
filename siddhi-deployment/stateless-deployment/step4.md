This section provides instructions on testing the stateless Siddhi Apps that's deployed in the previous step.

### Sending Events 

Use the following cURL command to send multiple HTTP requests. 

`
    curl -X POST \
    http://siddhi/power-surge-app-0/8080/checkPower \
    -H 'Accept: */*' \
    -H 'Content-Type: application/json' \
    -H 'Host: siddhi' \
    -d '{
          "deviceType": "dryer",
          "power": 600
        }'
`{{execute}}

### Viewing Logs 

The deployed Siddhi App will log the messages if the device type is `dryer` and power is greater than or equals to `600`W.

Following command can be used to view the logs from pod `power-surge-app-0`. 

`kubectl logs $(kubectl get pods | awk '{ print $1 }' | grep ^power-surge-app-0) | tail -n 10`{{execute}}

The processed events will be logged similar to the following log segment.

```
...
[2019-08-02 05:13:07,008]  INFO {io.siddhi.core.stream.output.sink.LogSink} - LOGGER : Event{timestamp=1564722787005, data=[dryer, 600], isExpired=false}
```

**Congratulations on successfully completing the scenario!**