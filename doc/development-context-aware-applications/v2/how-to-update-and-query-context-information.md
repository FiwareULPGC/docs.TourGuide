Processes running as part of your application architecture that update
context information using REST operations that the Context Broker GE
exports, are said to play a **Context Producer** role.  As an example,
let’s consider an application for rating restaurants (let’s call it
NiceEating). The client part of that application running on the
smartphone of users would play the role of Context Producer, enabling
them to rate restaurants.

On the other hand, processes running as part of your application
architecture that query context information using REST operations that
the Context Broker GE exports are said to play a **Context Consumer**
role.  Continuing with our NiceEating app, the mobile application
running on the smartphone of users and enabling them to query for the
rating of restaurants would play the role of Context Consumer.  Note
that a given part of your application may play both the Context Producer
and Context Consumer roles.  This would be the case of the mobile client
of the NiceEating app enabling end users to rate, and query about rates
of, restaurants.

Entities that would be relevant to the NiceEating application are of
type Restaurant, Client and Rating. For example, when a given user
scores a restaurant (e.g. in a scale from 0 to 5, “Client1234” scores
“4” for the “LeBistro” restaurant) the smartphone application plays the
Context Producer role **creating** a Rating entity by issuing the
following HTTP request :

    POST <cb_host>:<cb_port>/v2/Entities?options=keyValues
    {
      "type": "Review",
      "id": "review-Elizalde-34",
      "author": "JhonSmith",
      "itemReviewed": "Elizalde",
      "reviewBody": "Body of the review",
      "ratingValue": 4
    }

Each time a new Rating entity is created, the average rating for the
corresponding restaurant is recalculated by the application backend
which (playing also the role of Context Producer) **updates** the
Restaurant entity accordingly:

    PUT <cb_host>:<cb_port>/v2/Entities/Elizalde/attrs/aggregateRating?options=keyValues
      {
        "value": {
          "reviewCount": 1,
          "ratingValue": 3
        } 
      }

Finally, the user can get the information of a given Restaurant using
the smartphone application. In that case the application works as
Context Consumer, **querying** the Restaurant entity. For example, to get
the average\_scoring attribute, the client application could query for
it in the following way:

    GET <cb_host>:<cb_port>/v2/Entities/Elizalde/attrs/aggregateRating/value
    //getting a JSON response such as the following one:
     {
        "reviewCount": 1,
        "ratingValue": 3
      }

You can also obtain the values of all attributes of the "Elizalde"
restaurant in a single shot:

    GET <cb_host>:<cb_port>/v2/Entities/Elizalde/attrs?options=keyValues

    //getting a JSON response such as the following one:  

    {
        "id": "Elizalde",
        "type": "Restaurant",
        "address":
        {
            "type": "none",
            "value":
            {
                "@type": "postalAddress",
                "streetAddress": "Av. de la Universidad 30",
                "addressLocality": "Leganes",
                "addressRegion": "Madrid",
                "postalCode": 28911
            },
            "metadata":
            {
            }
        },
        "aggregateRating":
        {
            "type": "none",
            "value":
            {
                "reviewCount": 1,
                "ratingValue": 4
            },
            "metadata":
            {
            }
        },
        "capacity":
        {
            "type": "none",
            "value": 200,
            "metadata":
            {
            }
        },
        "department":
        {
            "type": "none",
            "value": "Franchise4",
            "metadata":
            {
            }
        },
        "description":
        {
            "type": "none",
            "value": "Restaurant description",
            "metadata":
            {
            }
        },
        "name":
        {
            "type": "none",
            "value": "Elizalde",
            "metadata":
            {
            }
        },
        "occupancyLevels":
        {
            "type": "none",
            "value":
            {
                "type": "PropertyValue",
                "timestamp": "2016-04-06T08:30:27.761Z",
                "name": "occupancyLevels",
                "value": 0
            },
            "metadata":
            {
            }
        },
        "priceRange":
        {
            "type": "none",
            "value": 25,
            "metadata":
            {
            }
        },
        "telephone":
        {
            "type": "none",
            "value": "912345678",
            "metadata":
            {
            }
        },
        "url":
        {
            "type": "none",
            "value": "http://www.example.com",
            "metadata":
            {
            }
        }
    }



Alternatively, if you want to get the **attributes as a key value instead
of as a vector**, you can use the keyValues parameter, in the
following way

    GET <cb_host>:<cb_port>/v2/Entities/LeBistro?options=keyValues

    //getting a JSON response such as the following one:
    
    {
        "id": "Elizalde",
        "type": "Restaurant",
        "address":
        {
            "@type": "postalAddress",
            "streetAddress": "Av. de la Universidad 30",
            "addressLocality": "Leganes",
            "addressRegion": "Madrid",
            "postalCode": 28911
        },
        "aggregateRating":
        {
            "reviewCount": 1,
            "ratingValue": 4
        },
        "capacity": 200,
        "department": "Franchise4",
        "description": "Restaurant description",
        "name": "Elizalde",
        "occupancyLevels":
        {
            "type": "PropertyValue",
            "timestamp": "2016-04-06T08:30:27.761Z",
            "name": "occupancyLevels",
            "value": 0
        },
        "priceRange": 25,
        "telephone": "912345678",
        "url": "http://www.example.com"
    }


