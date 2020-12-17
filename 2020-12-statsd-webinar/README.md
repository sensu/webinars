The information below will allow you to replicate the use of the DataDog Golang sample code that was presented during the webinar.

## Prerequisites

* Have a running [Sensu backend](https://docs.sensu.io/sensu-go/latest/get-started/)
* Have a running [InfluxDB](https://portal.influxdata.com/downloads/) instance
* Have a [handler defined](https://docs.sensu.io/sensu-go/latest/plugins/supported-integrations/influxdb/) for sending metrics to InfluxDB
* Have a running Sensu agent with the following changes to /etc/sensu/agent.yml (assuming handler above is named influxdb):

        statsd-event-handlers:
          - influxdb
* [Golang](https://golang.org) compiler installed
* [Grafana](https://grafana.com/get) instance running with the InfluxDB datasource

## Compiling the code

You should now be able to compile the Golang sample code and run it on the agent to submit the metrics to InfluxDB.

[DogStatsD example code](https://docs.datadoghq.com/developers/metrics/dogstatsd_metrics_submission/#distribution)

**NOTE:** At the time of this webinar the Golang code for the [set example](https://docs.datadoghq.com/developers/metrics/dogstatsd_metrics_submission/#set)
was incorrect and would fail to build.

* The `strconv.Itoa()` function will not work with the `i` variable being of type `float64`.
* The `rand.Intn(10)` value that is the first argument to the `time.Sleep()` function needs to be cast to a `time.Duration` type.

Here is the fixed code:

```golang
package main

import (
	"fmt"
	"log"
	"math/rand"
	"time"

	"github.com/DataDog/datadog-go/statsd"
)

func main() {
	statsd, err := statsd.New("127.0.0.1:8125")
	if err != nil {
		log.Fatal(err)
	}
	var i float64
	for {
		i += 1
		statsd.Set("example_metric.set", fmt.Sprintf("%f", i), []string{"environment:dev"}, 1)
		time.Sleep(time.Duration(rand.Intn(10)) * time.Second)
	}
}
```

## Grafana

To view graphs from the webinar in Grafana, using InfluxDB as the datasource, import [this dashboard](golang-grafana-dashboard.json) into Grafana.

