---
title: Common pitfalls
description: How to avoid common pitfalls within Velocity.
slug: velocity/dev/pitfalls
---

While we try to make the API a pleasure to use, there are the occasional rough edges, and you should
be aware of them.

## Accessing the API at construction time

In Velocity, plugin loading is split into two steps: construction and initialization. The code in
your plugin's constructor is part of the construction phase. There is very little you can do safely
during construction, especially as the API does not specify which operations are safe to run during
construction. Notably, you can't register an event listener in your constructor, because you need to
have a valid plugin registration, but Velocity can't register the plugin until the plugin has been
constructed.

To break this cycle, you should always wait for initialization, which is indicated when Velocity
fires the [`ProxyInitializeEvent`](jd:velocity:com.velocitypowered.api.event.proxy.ProxyInitializeEvent).
We can do things on initialization by adding a listener for this event, as shown below.
Note that Velocity automatically registers your plugin main class as a listener.

```java
@Subscribe
public void onProxyInitialization(ProxyInitializeEvent event) {
    // Do some operation demanding access to the Velocity API here.
    // For instance, we could register an event:
    server.getEventManager().register(this, new PluginListener());
}
```

## Audience operations are not fully supported

Velocity currently does not support all Audience operations of the Adventure API, so these operations should be handled on the backend.
Furthermore, playing sound was previously considered infeasible, as for versions below 1.19.3, a registry of hardcoded sound IDs is required.

| Operation                  | Supported |
| -------------------------- | ----------|
| Chat messages              | Yes       |
| Action bar messages        | Yes       |
| Titles                     | Yes       |
| Bossbars                   | Yes       |
| Tablist header and footer  | Yes       |
| Resource packs             | Yes       |
| Sound                      | No        |
| Book                       | No        |
