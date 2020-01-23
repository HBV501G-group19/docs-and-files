# documentation

This doc attempts to explain what the API accepts and responds with.

### /users/
The first two are the only endpoints that don't require authentication.

Note: Authentication/authorization wasn't added until after the 3rd. 
If you cloned it before or use an older commit there is no authentication enabled so you don't need to worry about it.

#### /users/register - POST
This endpoint only accepts POST requests. It's used to create a new user on the database.
Sending this:
```
{
  "username": "unique_username",
  "password": "passwordthatis5atleastcharacterslong"
}
```
The server will respond with the created user entity:
```
{
  "id": #id,
  "username": "unique_user",
}
```
#### /users/authenticate - POST


This endpoint also only accepts POST requests and is used to authenticate the user
```
{
  "username": "your_username",
  "password": "your_password"
}
```

It responds with the user entity in addition to a JWT that can be used for authentication. Each token lasts for 10 days.
```
{
  "id": #id,
  "username": "your_username",
  "token": "...."
}
```
The token should be added as a 'Bearer Token' into the authorization header of any request sent to the API.

#### /users - GET
Responds with a list of all user
```
[
  {
    "id": #id,
    "username": "unique_user",
    "password": "hashed version that should be marked transient :^)"
  },
  ...
]
```

#### /users/{id} - GET
Responds with the user corresponding to the id.

### /rides/
#### /rides - GET
Responds with a list of all rides
```
[
    {
        "id": 1,
        "driver": 2,
        "passengers": [],
        "created": "2019-11-07T15:50:37.433+0000",
        "upTimestampd": "2019-11-07T15:50:37.433+0000",
        "origin": {
            "type": "Point",
            "coordinates": [...]
        },
        "destination": {
            "type": "Point",
            "coordinates": [...]
        },
        "route": {
            "type": "LineString",
            "coordinates": [...]
        },
        "departureTime": "2019-10-25T20:45:00.000+0000",
        "duration": 50,
        "totalSeats": 1,
        "freeSeats": 1
    }
]
```

#### /rides/{id} - GET
Responds with the ride corresponding to the id.

#### /rides/create - POST
Stores a ride into the database.
Needs to have the following properties:
> `driver: id of the user that is driving`

> `departure_time: YYYY-MM-DD HH:mm:ss ISO-formatted string`

> `duration: integer`

> `origin: A geojson 2d point`,

> `destination: A geojson 2d point`,

> `route: A geojson linestring`

> `seats: integer`

> `passengers(optional): A json array with user ids. Cannot contain the driver's id`

```
{
	"driver_id":2,
	"passengers": [3, 1, 5],
	"departure_time": "2019-10-25 20:45:00",
	"duration": 50,
	"seats": 1,
	"origin": {
		"type": "Point",
		"coordinates": [-21.94695, 64.140015]
	},
	"destination":  {
		"type": "Point",
		"coordinates": [-21.724842, 64.164932]
	},
	"route": {
		"type": "LineString",
		"coordinates": [
			[-21.94695, 64.140015],
			[-21.724842, 64.164932]
		]
	}
}
```

Responds with the created route

```
{
    "id": 1,
    "driver": 2,
    "passengers": [],
    "created": "2019-11-07T15:50:37.433+0000",
    "upTimestampd": "2019-11-07T15:50:37.433+0000",
    "origin": {
        "type": "Point",
	...
    },
    "destination": {
        "type": "Point",
	...
    },
    "route": {
        "type": "LineString",
	...
    },
    "departureTime": "2019-10-25T20:45:00.000+0000",
    "duration": 50,
    "totalSeats": 1,
    "freeSeats": 1
}
```

#### /ors/geoname
Returns a geojson `feature` with interesting properties(mainly name information) a point very close to the provided point.
*This isn't very useful, probably don't have to use this at all since getting the name of the endpoints is handled when rides are created/directions are requested...*
```
{
    "coordinates": [double, double],
    "properties": {... gets passed to the returned feature...}
}
```

#### /ors/directions
Gets directions between 2 points
```
{
    "origin": {
        "type": "Point",
	...
    },
    "destination": {
        "type": "Point",
	...
    },
    "properties": {
    	"profile": "foot-walking"|"driving-car"
    }
}
```

#### /ors/geocode
Returns points of interest relating to the search string. `focus` is a point that ORS will focus the search around(it will return results that are at most within a 1000km radius).

```
{
   "focus": {
   	"type": "Point",
	...
   },
   "geocode": "string to search with"
}
```

