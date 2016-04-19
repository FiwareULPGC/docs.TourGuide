Processes running as part of your application architecture that update
context information using REST operations that the Context Broker GE
exports, are said to play a **Context Producer** role.  As an example,
let’s consider an application for reviewing restaurants (let’s call it
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
of the NiceEating app enabling end users to review and query about reviews
of restaurants.

Entities that would be relevant to the NiceEating application are of
type Restaurant, Client and Review. For example, when a given user
reviews a restaurant (e.g. in a scale from 0 to 5, “Client1234” scores
“4” for the “LeBistro” restaurant and describes his/her experience with "Cheap and nice place to eat.") the smartphone application plays the
Context Producer role **creating** a Review entity by issuing the
following HTTP request :

    POST <cb_host>:<cb_port>/v2/Entities?options=keyValues
    {
      "type": "Review",
      "id": "review-Elizalde-34",
      "author": "Client1234",
      "itemReviewed": "Elizalde",
      "reviewBody": "Cheap and nice place to eat.",
      "ratingValue": 4
    }

Each time a new Review entity is created, the average rating for the
corresponding restaurant is recalculated by the application backend
which (playing also the role of Context Producer) **updates** the
Restaurant entity accordingly:

    PUT <cb_host>:<cb_port>/v2/Entities/Elizalde/attrs/aggregateRating?options=keyValues
      {
        "value": {
          "reviewCount": 2,
          "ratingValue": 3
        } 
      }

Also, the user can get the information of a given Restaurant using
the smartphone application. In that case the application works as
Context Consumer, **querying** the Restaurant entity. For example, to get
the aggregateRating attribute, the client application could query for
it in the following way:

    GET <cb_host>:<cb_port>/v2/Entities/Elizalde/attrs/aggregateRating/value
    //getting a JSON response such as the following one:
     {
        "reviewCount": 1,
        "ratingValue": 3
      }

You can also obtain the values of all attributes of the "Elizalde"
restaurant in a single shot:

    GET <cb_host>:<cb_port>/v2/Entities/Elizalde

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



Alternatively, if you want to get the **attributes as a key value**, you can use the keyValues parameter, in the
following way:

    GET <cb_host>:<cb_port>/v2/Entities/Elizalde?options=keyValues

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


Finally the application can query entities by attribute content. For example, to get all restaurants with capacity greater than 50 the next request can be used:

    GET <cb_host>:<cb_port>/v2/Entities?type=Restaurant&q=capacity>50&options=keyValues 

getting a JSON such as:

    [
        {
            "id": "Elizalde",
            "type": "Restaurant",
            "address":
            {
                "type": "postalAddress",
                "streetAddress": "Cuesta de las Cabras Aldapa 2",
                "addressRegion": "Araba",
                "addressLocality": "Alegría-Dulantzi",
                "postalCode": "01240"
            },
            "aggregateRating":
            {
                "reviewCount": 1,
                "ratingValue": 3
            },
            "capacity": 120,
            "department": "Franchise3",
            "description": "Restaurante de estilo sidrería ubicado en Alegria-Dulantzi. Además de su menú del día y carta, también ofrece menú de sidrería. El menú del día cuesta 9 euros. Los fines de semana la especialidad de la casa son las alubias con sacramentos. En lo que a bebidas se refiere, hay una amplia selección además de la sidra. Cabe destacar que se puede hacer txotx. La capacidad del establecimiento es de 50 personas pero la sidrería no dispone de aparcamiento.%5cn%5cnHORARIO: %5cn%5cnLunes a domingo: 9:00-17:00 y 19:00-23:00.",
            "occupancyLevels": 0,
            "position": "42.8404625, -2.5123277",
            "priceRange": 0,
            "telephone": "945 400 868"
        },
        {
            "id": "Arabako Txakolina, S.L.",
            "type": "Restaurant",
            "address":
            {
                "type": "postalAddress",
                "streetAddress": "Maskuribai Kalea",
                "addressRegion": "Araba",
                "addressLocality": "Amurrio",
                "postalCode": "01470"
            },
            "aggregateRating":
            {
                "reviewCount": 1,
                "ratingValue": 4
            },
            "capacity": 160,
            "department": "Franchise4",
            "description": "Bodega adscrita a la Denominación de Origen Arabako Txakolina y que elabora el 95% de la producción total de esta denominación. Sus caldos se comercializan bajo las marcas Xarmant y Maskuribai, ambas elaboradas con uvas de las variedades Hondarribi Zuri, Gross Manseng, Petit Corbu. Las instalaciones cuentan con todo el equipo necesario para la adecuada aplicación de las técnicas ecológicas y para conseguir un vino de calidad, y pueden visitarse concertando cita previa.",
            "occupancyLevels": 0,
            "position": "43.047415, -3.0007716",
            "priceRange": 0,
            "telephone": "645 713 355",
            "url": "http://www.xarmant.net/"
        },
        {
            "id": "El Txakoli",
            "type": "Restaurant",
            "address":
            {
                "type": "postalAddress",
                "streetAddress": "Aldai Kalea 40",
                "addressRegion": "Araba",
                "addressLocality": "Amurrio",
                "postalCode": "01470"
            },
            "aggregateRating":
            {
                "reviewCount": 1,
                "ratingValue": 4
            },
            "capacity": 200,
            "department": "Franchise4",
            "description": "La bodega más antigua y pequeña de Álava se encuentra situada en el agroturismo El Txakoli. Su txakoli, de nombre ¿mahatxuri¿, se elabora a partir de las variedades hondarribi zuri y folie blanc y tiene la Denominación de Origen Arabako Txakolina. El Txakoli tiene una capacidad de 4000 litros.",
            "occupancyLevels": 0,
            "position": "43.04599899999999, -3.0046968",
            "priceRange": 0,
            "telephone": "945 890 703"
        }
    ]
