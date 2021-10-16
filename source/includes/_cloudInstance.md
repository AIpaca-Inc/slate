# Cloud Instance

We will use the word "machine" and "server" interchangeably with "instance" in the following content.

## Check Capacity

```python
available_machine()
```

**Capacity**: the amount of instances that are requestable.

## Spot Vs On-demand Instance

### Machine id

By simply adding ".od" after machine id to convert instance type from spot to on-demand (e.g. _p2.xlarge_ is spot and _p2.xlarge.od_ is on-demand).

### Pricing

Spot instances are usually 70% cheaper than their corresponding on-demand instances.

### Availability

As a tradeoff, spot instance requests are not always fulfilled. We defined a term **"availability"** as the success probability of
instance request.

Clearly, the availability of on-demand instances are always 100%. Therefore, you are guaranteed to get a on-demand
instance as long as there is enough capacity.

The availability of spot instances are varied by instance types and request time. In general, we found more powerful
instance types have less availability (e.g. p3.2xlarge is less available than p2.xlarge). Meanwhile, spot instances are
usually more available during non-working hours.

### Reliability

Spot instances have chance to be interrupted by AWS. On-demand instances are always stable.

### Setup Speed

The **[stop](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)** feature allows on-demand instances to be set up faster than spot instances. For the first time an **new** instance was requested, tensorflow always needs around 5 minutes to gear up before training start. Unlike spot instances can only be [**terminated**](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html), on-demand instances are stoppable, which reduce their second-time gear up time to 0.5 minutes. Both instance types can set [Cooling Period](#cooling-period), which, of course, won't have any gear up time at all.

Except longer gear up time, a spot instance needs extra time to fulfill spot request.

The following table compares the job timelines (refer to [plot_timeline()](#plot_timeline)) of new, stopped, and cooling instances when training the same model & data on the same instance.

| Type    | gear up time (minutes) | timeline                                                                                        |
| ------- | ---------------------- | ----------------------------------------------------------------------------------------------- |
| new     | 5                      | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/new_instance_timeline.html)     |
| stopped | 0.5                    | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/stopped_instance_timeline.html) |
| cooling | 0                      | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/cooling_server_timeline.html)   |
