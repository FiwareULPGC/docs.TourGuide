Apart from getting information using queries in a synchronous way (as
illustrated in the “How to update and query context information” section
above), Context Consumers can also get context information in an
asynchronous way using notifications. In this scenario, the Context
Broker GE is “programmed” to send notifications upon given conditions
(specified in the subscription request).

In the case of NiceEating, the application backend could use a
subscription so each time a new review is cast by any user, the backend
gets notified (in order to recalculate restaurant average rating and
publish it back in the Context Broker GE).

    POST <cb_host>:<cb_port>/v2/subscriptions 
    {
      "description": "Update average rating",
      "subject": {
        "entities": [
          {
            "idPattern": ".*",
            "type": "Review"
          }
        ],
        "condition": {
          "attributes": [
            "ratingValue"
          ]
        }
      },
      "notification": {
        "callback": "http://backend.niceeating.foo.com:1028/ratings",
        "attributes": [
          "ratingValue",
          "itemReviewed"
        ],
        "throttling": 5
      },
      "expires": "2020-04-05T14:00:00.00Z"
    }



Another case would be an application that subscribes to changes in
average ratings of a given restaurant. This may be useful for restaurant
owners in other to know how their restaurants score is evolving.

    POST <cb_host>:<cb_port>/v2/subscriptions

    {
      "description": "Get aggregateRating for Elizalde",
      "subject": {
        "entities": [
          {
            "id": "Elizalde",
            "type": "Restaurant"
          }
        ],
        "condition": {
          "attributes": [
            "aggregateRating"
          ]
        }
      },
      "notification": {
        "callback": "http://myapp.foo.com:1028/restaurant_average_scorings",
        "attributes": [
          "aggregateRating"
        ],
        "throttling": 5
      },
      "expires": "2020-04-05T14:00:00.00Z"
    }

 
