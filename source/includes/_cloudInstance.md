# Cloud Instance

We will use the word "machine" and "server" interchangeably with "instance" in the following content.

## Check Capacity

```python
available_machine()
```

**Capacity**: the amount of instances that are requestable.

## Spot Vs On-demand Instance

Spot instances are usually 70% cheaper than their corresponding on-demand instances. As a tradeoff, spot instance
requests are not always fulfilled. We defined a term **"availability"** as the success probability of
instance request.

Clearly, the availability of on-demand instances are always 100%. Therefore, you are guaranteed to get a on-demand
instance as long as there is enough capacity.

The availability of spot instances are varied by instance types and request time. In general, we found more powerful
instance types have less availability (e.g. p3.2xlarge is less available than p2.xlarge). Meanwhile, spot instances are
usually more available during non-working hours.

By simply adding ".od" after instance types to make them from spot to on-demand.