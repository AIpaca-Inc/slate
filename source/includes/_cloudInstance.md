# Cloud Instance

We will use the word "machine" and "server" interchangeably with "instance" in the following content.

## Check Capacity

**Capacity**: the number of instances that are requestable.

Instance capacity can be checked on either [AIbro Marketplace](https://aipaca.ai/marketplace) or `aibro.comm.available_machines()`

```python
def available_machines()

# Sample Output:
# Machine Id: g4dn.12xlarge  GPU Type: 4xT4     num_vCPU: 48    cost: 1.43      capacity: 2 availability: 32.0%
# Machine Id: g4dn.12xlarge.od GPU Type: 4xT4     num_vCPU: 48    cost: 3.91      capacity: 3 availability: 100%
# Machine Id: g4dn.16xlarge  GPU Type: 1xT4     num_vCPU: 64    cost: 1.31      capacity: 2 availability: 13.0%
# Machine Id: g4dn.16xlarge.od GPU Type: 1xT4     num_vCPU: 64    cost: 4.35      capacity: 2 availability: 100%
# Machine Id: g4dn.4xlarge   GPU Type: 1xT4     num_vCPU: 16    cost: 0.36      capacity: 8 availability: 86.0%
```

## Spot Vs On-demand Instance

### Machine id

By simply adding ".od" after machine id to convert instance type from spot to on-demand (e.g. _p2.xlarge_ is spot and _p2.xlarge.od_ is on-demand).

### Pricing

Spot instances are usually 70% cheaper than their corresponding on-demand instances.

### Availability

As a tradeoff, spot instance requests are not always fulfilled. We defined the term **"availability"** as the success probability of
instance request.

Clearly, the availability of on-demand instances are always 100%. Therefore, you are guaranteed to get an on-demand
instance as long as there is enough capacity.

The availabilities of spot instances is varied by instance types and request time. In general, we found more powerful
instance types have less availability (e.g. p3.2xlarge is less available than p2.xlarge). Meanwhile, spot instances are
usually more available during non-working hours.

### Reliability

Spot instances have a chance to be interrupted by AWS. On-demand instances are always stable.

### Setup Speed

The **[stop](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)** feature allows on-demand instances to be set up faster than spot instances. For the first time a **new** instance was requested, tensorflow always needs around 5 minutes to gear up before training starts. Unlike spot instances that can only be [**terminated**](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html), on-demand instances are stoppable, which reduces their second-time gear up time to 0.5 minutes. Both instance types can set [Cooling Period](#cooling-period), which, of course, won't have any gear up time at all.

Except for longer gear up time, a spot instance needs extra time to fulfill its request.

The following table compares the job timelines (refer to [plot_timeline()](#plot_timeline)) of new, stopped, and cooling instances when training the same model & data on the same instance.

| Type    | gear up time (minutes) | timeline                                                                                        |
| ------- | ---------------------- | ----------------------------------------------------------------------------------------------- |
| new     | 5                      | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/new_instance_timeline.html)     |
| stopped | 0.5                    | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/stopped_instance_timeline.html) |
| cooling | 0                      | [Timeline](https://aibro-user-timeline.s3.amazonaws.com/example/cooling_server_timeline.html)   |
