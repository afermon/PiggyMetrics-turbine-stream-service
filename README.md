# Piggy Metrics (Kubernetes) - Turbine Stream Service

[![CircleCI](https://circleci.com/gh/afermon/PiggyMetrics-turbine-stream-service.svg?style=svg)](https://circleci.com/gh/afermon/PiggyMetrics-turbine-stream-service) [![codecov](https://codecov.io/gh/afermon/PiggyMetrics-turbine-stream-service/branch/master/graph/badge.svg)](https://codecov.io/gh/afermon/PiggyMetrics-turbine-stream-service) [![GitHub license](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/afermon/PiggyMetrics-turbine-stream-service/blob/master/LICENCE)

In this project configuration, each microservice with Hystrix on board pushes metrics to Turbine via Spring Cloud Bus (with AMQP broker). The Monitoring project is just a small Spring boot application with [Turbine](https://github.com/Netflix/Turbine) and [Hystrix Dashboard](https://github.com/Netflix/Hystrix/tree/master/hystrix-dashboard).

See below [how to get it up and running](https://github.com/afermon/PiggyMetrics-Kubernetes#how-to-run-all-the-things).

Let's see our system behavior under load: Account service calls Statistics service and it responses with a vary imitation delay. Response timeout threshold is set to 1 second.

![Hystrix Dashboard](https://github.com/afermon/PiggyMetrics-Kubernetes/raw/master/resources/hystrix-dashboard.png)

![0ms](https://github.com/afermon/PiggyMetrics-Kubernetes/raw/master/resources/hystrix-0ms-delay.gif)	| ![500ms](https://github.com/afermon/PiggyMetrics-Kubernetes/raw/master/resources/hystrix-500ms-delay.gif) | ![800ms](https://github.com/afermon/PiggyMetrics-Kubernetes/raw/master/resources/hystrix-800ms-delay.gif) | ![1100ms](https://github.com/afermon/PiggyMetrics-Kubernetes/raw/master/resources/hystrix-1100ms-delay.gif)
--- |--- |--- |--- |
| `0 ms delay` | `500 ms delay` | `800 ms delay` | `1100 ms delay`
| Well behaving system. The throughput is about 22 requests/second. Small number of active threads in Statistics service. The median service time is about 50 ms. | The number of active threads is growing. We can see purple number of thread-pool rejections and therefore about 30-40% of errors, but circuit is still closed. | Half-open state: the ratio of failed commands is more than 50%, the circuit breaker kicks in. After sleep window amount of time, the next request is let through. | 100 percent of the requests fail. The circuit is now permanently open. Retry after sleep time won't close circuit again, because the single request is too slow.

For more information please refer to the main repository [afermon/PiggyMetrics-Kubernetes](https://github.com/afermon/PiggyMetrics-Kubernetes)

## References
* Forked from [sqshq/PiggyMetrics](https://github.com/sqshq/PiggyMetrics)