Processes running as part of your application architecture that update
context information using REST operations that the Context Broker GE
exports, are said to play a **Context Producer** role.  As an example,
let’s consider an application for rating restaurants (let’s call it
NiceEating). The client part of that application running on the
smartphone of users would play the role of Context Producer, enabling
them to review restaurants.

On the other hand, processes running as part of your application
architecture that query context information using REST operations that
the Context Broker GE exports are said to play a **Context Consumer**
role.  Continuing with our NiceEating app, the mobile application
running on the smartphone of users and enabling them to query for the
reviews of restaurants would play the role of Context Consumer.  Note
that a given part of your application may play both the Context Producer
and Context Consumer roles.  This would be the case of the mobile client
of the NiceEating app enabling end users to review, and query about reviews
of, restaurants.

Entities that would be relevant to the NiceEating application are of
type Restaurant, Client and Reviews. For example, when a given user
reviews a restaurant (e.g. in a scale from 0 to 5, “Client1234” rates
“4” for the “Elizalde” restaurant and describes his/her experience with "Cheap and nice place to eat.") the smartphone application plays the
Context Producer role **creating** a Review entity by issuing the
following HTTP request:

    POST <cb_host>:<cb_port>/v1/contextEntities/type/Review/id/review-Elizalde-34
    {
      "attributes" : [{
          "name" : "ratingValue",
          "type" : "integer",
          "value" : "4"
        },
        {
          "name" : "author",
          "type" : "string",
          "value" : "Client1234"
        },
        {
          "name" : "itemReviewed",
          "type" : "string",
          "value" : "Elizalde"
        },
        {
          "name" : "reviewBody",
          "type" : "string",
          "value" : "Cheap and nice place to eat."
        }
      ]
    }

Each time a new Review entity is created, the average rating for the
corresponding restaurant is recalculated by the application backend
which (playing also the role of Context Producer) **updates** the
Restaurant entity accordingly:

    PUT <cb_host>:<cb_port>/v1/contextEntities/type/Restaurant/id/Elizalde/attributes/aggregateRating
    {
       "value" : "4.2"
       "reviewCount": 1,
    }

Finally, the user can get the information of a given Restaurant using
the smartphone application. In that case the application works as
Context Consumer, **querying** the Restaurant entity. For example, to get
the aggregateRating attribute, the client application could query for
it in the following way:

    GET <cb_host>:<cb_port>/v1/contextEntities/type/Restaurant/id/Elizalde/attributes/aggregateRating
    //getting a JSON response such as the following one:
    {
      "attributes" : [
      {
        "name": "aggregateRating",
        "type": "float",
        "value": "3.2"
      }
      ],
      "statusCode" : {
        "code" : "200",
        "reasonPhrase" : "OK"
      }
    } 

You can also obtain the values of all attributes of the "Elizalde"
restaurant in a single shot:


    GET <cb_host>:<cb_port>/v1/contextEntities/type/Restaurant/id/Elizalde
    {
      "contextElement":
        {
          "type": "Restaurant",
          "isPattern": "false",
          "id": "Elizalde",
          "attributes":
          [
            {
              "name": "address",
              "type": "",
              "value":
              {
                "type": "postalAddress",
                "streetAddress": "Cuesta de las Cabras Aldapa 2",
                "addressRegion": "Araba",
                "addressLocality": "Alegría-Dulantzi",
                "postalCode": "01240"
              }
            },
            {
              "name": "aggregateRating",
              "type": "",
              "value":
              {
                "reviewCount": 1,
                "ratingValue": 3
              }
            },
            {
              "name": "capacity",
              "type": "PropertyValue",
              "value": 120,
              "metadatas":
              [
                {
                  "name": "name",
                  "type": "",
                  "value": "capacity"
                }
              ]
            },
            {
              "name": "department",
              "type": "",
              "value": "Franchise3"
            },
            {
              "name": "description",
              "type": "",
              "value": "Restaurante de estilo sidrería ubicado en Alegria-Dulantzi. Además de su menú del día y carta, también ofrece menú de sidrería. El menú del día cuesta 9 euros. Los fines de semana la especialidad de la casa son las alubias con sacramentos. En lo que a bebidas se refiere, hay una amplia selección además de la sidra. Cabe destacar que se puede hacer txotx. La capacidad del establecimiento es de 50 personas pero la sidrería no dispone de aparcamiento.%5cn%5cnHORARIO: %5cn%5cnLunes a domingo: 9:00-17:00 y 19:00-23:00."
            },
            {
              "name": "occupancyLevels",
              "type": "PropertyValue",
              "value": 0,
              "metadatas":
              [
                {
                  "name": "timestamp",
                  "type": "",
                  "value": ""
                },
                {
                  "name": "name",
                  "type": "",
                  "value": "occupancyLevels"
                }
              ]
            },
            {
              "name": "position",
              "type": "coords",
              "value": "42.8404625, -2.5123277",
              "metadatas":
              [
                {
                  "name": "location",
                  "type": "string",
                  "value": "WGS84"
                }
              ]
            },
            {
              "name": "priceRange",
              "type": "",
              "value": 0
            },
            {
              "name": "telephone",
              "type": "",
              "value": "945 400 868"
            }
          ]
        },
        "statusCode":
        {
            "code": "200",
            "reasonPhrase": "OK"
        }
    }



Alternatively, if you want to get the **attributes as a key map instead
of as a vector**, you can use the attributesFormat parameter, in the
following way

    GET <cb_host>:<cb_port>/v1/contextEntities/type/Restaurant/id/Elizalde?attributesFormat=object   
    {
      "contextElement" : {
        "type" : "Restaurant",
        "isPattern" : "false",
        "id" : "Elizalde",
        "attributes" : {
          "address" : {
            "type" : "",
            "value" : {
              "type" : "postalAddress",
              "streetAddress" : "Cuesta de las Cabras Aldapa 2",
              "addressRegion" : "Araba",
              "addressLocality" : "Alegría-Dulantzi",
              "postalCode" : "01240"
            }
          },
          "aggregateRating" : {
            "type" : "",
            "value" : {
              "reviewCount" : 1,
              "ratingValue" : 3
            }
          },
          "capacity" : {
            "type" : "PropertyValue",
            "value" : 120,
            "metadatas" : [
              {
                "name" : "name",
                "type" : "",
                "value" : "capacity"
              }
            ]
          },
          "department" : {
            "type" : "",
            "value" : "Franchise3"
          },
          "description" : {
            "type" : "",
            "value" : "Restaurante de estilo sidrería ubicado en Alegria-Dulantzi. Además de su menú del día y carta, también ofrece menú de sidrería. El menú del día cuesta 9 euros. Los fines de semana la especialidad de la casa son las alubias con sacramentos. En lo que a bebidas se refiere, hay una amplia selección además de la sidra. Cabe destacar que se puede hacer txotx. La capacidad del establecimiento es de 50 personas pero la sidrería no dispone de aparcamiento.%5cn%5cnHORARIO: %5cn%5cnLunes a domingo: 9:00-17:00 y 19:00-23:00."
          },
          "occupancyLevels" : {
            "type" : "PropertyValue",
            "value" : "0",
            "metadatas" : [
              {
                "name" : "timestamp",
                "type" : "",
                "value" : ""
              },
              {
                "name" : "name",
                "type" : "",
                "value" : "occupancyLevels"
              }
            ]
          },
          "position" : {
            "type" : "coords",
            "value" : "42.8404625, -2.5123277",
            "metadatas" : [
              {
                "name" : "location",
                "type" : "string",
                "value" : "WGS84"
              }
            ]
          },
          "priceRange" : {
            "type" : "",
            "value":"0"
          },
          "telephone" : {
            "type" : "",
            "value" : "945 400 868"
          }
        }
      },
      "statusCode" : {
        "code" : "200",
        "reasonPhrase" : "OK"
      }
    }
