---
layout: docs
title: Data API
---

# Loading Data through Data API

Data API is designed to import and collect data into PredictionIO in event-based style.
All PredictionIO-compliant engines support the data store and data format used by the Data API.

> You may also [modify DataSource](/cookbook/existingdatasource.html) of an engine to read data directly from your existing data store.

## Launching Data API Server

```
$ cd $PIO_HOME
$ bin/pio dataapi
```
### Check server status

```
$ curl -i -X GET http://localhost:8081
```

Sample response:

```
HTTP/1.1 200 OK
{"status":"alive"}
```

## Using Data API

You may connect to the Data API with HTTP request or by using one of the `PredictionIO SDKs`.

### Create event

Event of one entity:

<div class="codetabs">
<div data-lang="HTTP">
{% highlight bash %}
$ curl -i -X POST http://localhost:8081/events \
-H "Content-Type: application/json" \
-d '{
  "event" : "my_event",
  "entityType" : "user"
  "entityId" : "uid",
  "properties" : {
    "prop1" : 1,
    "prop2" : "value2",
    "prop3" : [1, 2, 3],
    "prop4" : true,
    "prop5" : ["a", "b", "c"],
    "prop6" : 4.56
  }
  "eventTime" : "2004-12-13T21:39:45.618Z",
  "tags" : ["tag1", "tag2"],
  "appId" : 4,
  "predictionKey" : "my_prediction_key"
}'
{% endhighlight %}
</div>
<div data-lang="Python-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
<div data-lang="Ruby-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
<div data-lang="Java-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
</div>

Event between two entities (with **targetEntityId**):

<div class="codetabs">
<div data-lang="HTTP">
{% highlight bash %}
$ curl -i -X POST http://localhost:8081/events \
-H "Content-Type: application/json" \
-d '{
  "event" : "my_event",
  "entityType" : "user",
  "entityId" : "uid",
  "targetEntityType" : "item",
  "targetEntityId" : "iid",
  "properties" : {
    "prop1" : "value1",
    "prop2" : "value2"
  }
  "eventTime" : "2004-12-13T21:39:45.618Z",
  "tags" : ["tag1", "tag2"],
  "appId" : 4,
  "predictionKey" : "my_prediction_key"
}'
{% endhighlight %}
</div>
<div data-lang="Python-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
<div data-lang="Ruby-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
<div data-lang="Java-SDK">
{% highlight bash %}
(TODO)
{% endhighlight %}
</div>
</div>


Sample response:

```
HTTP/1.1 201 Created
{"eventId":"pBXkP-GkRfShh-xIRTIG2A"}
```

The following fields are optional:
* **eventTime** : current time will be used if it's not specified
* **tags**: empty list of tag will be used if it's not specified
* **predictionKey**
* **properties**


Note:
* **entityType + entityId** becomes the unique identifier of the entity. For example, you may have entityType named "user". In this entityType, you have different entities with different entityId, say 1 and 2. Then user-1 and user-2 uniquely identifies these two entities.

* **properties** can be associated with either the **entity** or the **event**.

* **properties** associated with **entity**: For example, entity user-1 may have properties of "birthday" and "address". To set and unset properties for the entity, use special event **$set** and **$unset** to create an event of the entity. For example,

  ```
  '{
    "event" : "$set",
    "entityType" : "user"
    "entityId" : "1",
    "properties" : {
      "birthday" : "1984-10-11",
      "address" : "1234 Street, San Francisco, CA 94107"
    }
  }'
  ```

* **properties** associated with **events**: For example, user-1 may have "rate" event on item-1 with rating value of 4.5.

  ```
  '{
    "event" : "rate",
    "entityType" : "user"
    "entityId" : "1",
    "targetEntityType" : "item",
    "targetEntityId" : "1"
    "properties" : {
      "rating" : 4.5
    }
  }'
  ```

## For Debug Purpose

### Get event

replace *your_eventId* by the returned eventId:

```
$ curl -i -X GET http://localhost:8081/events/<your_eventId> \
-H "Content-Type: application/json"
```

### Delete event

```
$ curl -i -X DELETE http://localhost:8081/events/<your_eventId>
```

### Get all events of appId
(*use cautiously*)

```
$ curl -i -X GET http://localhost:8081/events?appId=<your_appId> \
-H "Content-Type: application/json"
```

### Delete all events of appId
```
$ curl -i -X DELETE http://localhost:8081/events?appId=<your_appId>
```

### Get all events of appId within time range
* eventTime >= startTime:

```
$ curl -i -X GET http://localhost:8081/events?appId=<your_appId>&startTime=<time in ISO8601 format>
```
* eventTime < untilTime:

```
$ curl -i -X GET http://localhost:8081/events?appId=<your_appId>&untilTime=<time in ISO8601 format>
```

* eventTime >= startTime && eventTime < untilTime:

```
$ curl -i -X GET http://localhost:8081/events?appId=2&startTime=<time in ISO8601 format>&untilTime=<time in ISO8601 format>
```

Example,

```
$ curl -i -X GET http://localhost:8081/events?appId=2&startTime=2014-08-30T08:45:51.566Z&untilTime=2014-08-30T08:45:51.591Z
```