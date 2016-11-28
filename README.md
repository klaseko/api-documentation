# API Reference

## Client Documentation
The Klaseko API has two (2) environments `staging` and `production`.
The following endpoints address our two environments:
* **Staging** (for testing): `https://api-test.klaseko.com`
* **Production** (live transactions): `https://api.klaseko.com`

The client must make an HTTP request with the corresponding HTTP method (or "verb") to the endpoint that the client needs. For example, this is what a new payment operation will look like:

`POST https://api.klaseko.com/enrollment`

For the request to be complete, make sure the client has the appropriate HTTP headers and a valid JSON payload.



### Authentication & Headers

The Klaseko Payment Switch API uses [OAuth 2.0](https://oauth.net/2/). It is a web standard authorization framework/protocol described in [RFC 6749](https://tools.ietf.org/html/rfc6749). Klaseko will be providing all authorized clients with a `username` and `password`.

The client must use their username and password to obtain an access token.

#### Obtaining a username and password
Klaseko's API is currently in a closed beta. It is only open to select merchants and clients. Please contact us if you would like to connect.

#### Obtaining an access token
Access tokens are credentials used to access protected resources.  An access token is a string representing an authorization issued to the client. ([source](https://tools.ietf.org/html/rfc6749#section-1.4))

To obtain an access token, the client must make a request to the token endpoint

`POST https://api.klaseko.com/auth/sign_in`

Requires `email` and `password` as params. This route will return a JSON representation of the User model on successful login along with the access-token and client in the header of the response.

Sample Request:
```
curl -X POST
     -H "Content-Type: application/json"
     -d '{
       "email": "client@klaseko.com",
       "password": "password"
     }' "http://api.localhost.com/auth/sign_in"
```
Response:

| Header | Value |
|-------|--------|
| Access-Token | v4UIjjS0b0VgGOGVmqrv9g |
| Client | SPQrt4_3q0WlMsLPlbtssg |
| Expiry | 1480417124 |
| Token-Type | Bearer |
| Uid | client@klaseko.com |

```json
{
  "data": {
    "uid": "client@klaseko.com",
    "name": "Klaseko Enroll Client",
    "image": "https://s3.amazonaws.com/asdkj32/ashaf.jpg",
    "email": "client@klaseko.com"
  }
}
```

## Requests
All request to the API must contain `access-token`, `client`, `token-type`, and `uid` that was returned by the API after sign in.

## Getting School Information

### Side Loading
You may include related resources in one API call by using the `include` URL param. See example below.

### Schools

`GET /v1/schools` returns a list of all schools

`GET /v1/schools/:slug` returns details about a school given a slug or id.

**Example:**

`GET /v1/schools/webenrollments?include=venues,programs`

```json
{
  "school":{
    "name":"Yamaha Music School",
    "nickname":"yamahamusicschooltest",

    ...

    "venues":[
      { "name":"Buendia cor. Reposo", ... },
      { "name":"Manila Branch", ... }
    ],
    "programs":[
      { "name":"Guitar 101", ... },
      { "name":"Piano Basics", ... }
    ]
  },
  "status":"success"
}
```
> Some of the data has been concatenated for brevity and simplicity of the documentation.


### Venues

`GET /v1/venues` returns a list of all venues

`GET /v1/venues/:slug` returns details about a venue given a slug or id.

**Example:**

`GET /v1/venues/buendia-cor-reposo?include=venue_programs,schedules,schedule_records,programs,holidays`

```json
{
  "venue":{
    "name":"Buendia cor. Reposo",

    ...

    "venue_programs":[
      {
        "venue_id":100,
        "program_id":168,
        "sessions":20,
        "session_length":1,
        "price":"2000.0",
        "unit_time":"hours",
        "discounted":false,
        "discounts":[

        ],
        "deleted_at":null
      },
      {
        "venue_id":100,
        "program_id":173,
        "sessions":20,
        "session_length":1,
        "price":"5000.0",
        "unit_time":"hours",
        "discounted":false,
        "discounts":[

        ],
        "deleted_at":null
      }
    ],
    "schedules":[
      {
        "start_date":"2016-11-14",
        "end_date":"2016-12-09",
        "start_time":"2000-01-01T06:00:00.000Z",
        "end_time":"2000-01-01T07:00:00.000Z",
        "days":[
          "mon",
          "tue",
          "wed",
          "thu",
          "fri"
        ],
        "max_students":20,
        "settings":{
          "color":{
            "value":"rgb(81, 183, 73)"
          },
          "name":{
            "value":"Piano Basics"
          }
        },
        "active":true,
        "kind":"batched",
        "venue_program_id":188,
        "slots_available":20,
      },
      {
        "start_date":"2016-11-07",
        "end_date":"2016-12-21",
        "start_time":"2000-01-01T05:00:00.000Z",
        "end_time":"2000-01-01T06:00:00.000Z",
        "days":[
          "mon",
          "wed",
          "fri"
        ],
        "max_students":20,
        "settings":{
          "color":{
            "value":"teal"
          },
          "name":{
            "value":"Guitar 101"
          }
        },
        "active":true,
        "kind":"batched",
        "venue_program_id":183,
        "slots_available":20,
      }
    ],
  },
  "status":"success"
}
```

## Refreshing Schedules

A schedule's available slots will change as enrollments are coming in, you may want to refresh
the schedules that are being displayed on your platform.

`GET /venue_programs/:venue_program_id/available_schedules` will return schedules with available schedules.

**Sample Request**

`GET /v1/venue_programs/188/available_schedules`

**Response**
```json
{
  "schedules":[
    {
      "start_date":"2016-11-14",
      "end_date":"2016-12-09",
      "start_time":"2000-01-01T06:00:00.000Z",
      "end_time":"2000-01-01T07:00:00.000Z",
      "days":[
        "mon",
        "tue",
        "wed",
        "thu",
        "fri"
      ],
      "max_students":20,
      "settings":{
        "color":{
          "value":"rgb(81, 183, 73)"
        },
        "name":{
          "value":"Piano Basics"
        }
      },
      "active":true,
      "kind":"batched",
      "venue_program_id":188,
      "slots_available":20,
    }
  ],
  "status":"success"
}
```

With all these information, you can now proceed to create an enrollment.

## Enrollments
New Enrollment

Required fields:
- school_id
- venue_id
- email
- mobile_no
- source

> Use 'online' as value for source.

Non-required fields:
- info

**Sample Payload:**

`POST /v1/enrollments`
```json
{
  "enrollment": {
    "school_id":108,
    "venue_id":100,
    "source":"online",
    "info":[],
    "email":"gerard@klaseko.com",
    "mobile_no":"09166200691"
  }
}
```

**Response**
```json
{
  "enrollment":{
    "id":863,
    "school_id":108,
    "venue_id":100,
    "program_id":null,
    "course_id":null,
    "venue_program_id":null,
    "token":"0878457770a16",
    "ref_no":"KL1XW3S",
    "total":"0.0",
    "info":null,
    "email":"gerard@klaseko.com",
    "status":"new",
    "fields":null,
    "mobile_no":"09166200691",
    "amount_paid":"0.0",
    "balance":"0.0",
    "discount":"0.0",
    "expiration":"2016-11-28T19:32:08.133+08:00",
    "created_at":"2016-11-28T19:17:08.151+08:00",
    "updated_at":"2016-11-28T19:17:08.151+08:00",
    "slug":"kl1xw3s",
    "fees":{

    },
    "confirmed_at":null,
    "payment_method":null,
    "enrolled_at":"2016-11-28T19:17:08.133+08:00",
    "source":"online",
    "expired":false
  },
  "status":"success"
}
```

## Enrollees
New Enrollee

`POST /v1/enrollees`

Required Fields:
- school_id
- venue_id
- enrollment_id
- venue_program_id
- schedule_id
- first_name
- last_name
- birthdate
- gender
- start_date
- days

An enrollee needs a related enrollment. Pass the `enrollment_id` of the previously created enrollment.

**Sample Payload:**
```json
{
  "enrollee":{
    "school_id":108,
    "venue_id":100,
    "enrollment_id":863,
    "start_date":"2016-11-07",
    "days":[
      "mon",
      "wed",
      "fri"
    ],
    "info":[

    ],
    "birthdate":"1996-04-19T00:00:00+08:00",
    "first_name":"Gerard",
    "last_name":"Cruz",
    "gender":"male",
    "venue_program_id":183,
    "schedule_id":271,
    "id":830
  }
}
```

**Response**
```json
{
  "enrollee":{
    "id":830,
    "school_id":108,
    "venue_id":100,
    "program_id":null,
    "venue_program_id":183,
    "course_id":null,
    "schedule_id":271,
    "enrollment_id":863,
    "birthdate":"1996-04-19",
    "gender":"male",
    "status":null,
    "info":null,
    "fees":{
      "venue_program":"2000.0",
      "registration_fee":0.0
    },
    "total":"2000.0",
    "first_name":"Gerard",
    "last_name":"Cruz",
    "start_date":"2016-11-07",
    "days":[
      "mon",
      "wed",
      "fri"
    ],
    "created_at":"2016-11-28T19:21:11.983+08:00",
    "updated_at":"2016-11-28T19:21:25.081+08:00",
    "klaseko_id":"yamahamusicschooltest2016264095",
    "slug":"yamahamusicschooltest2016264095",
    "dates":[
      "2016-11-07",
      "2016-11-09",
      "2016-11-11",
      "2016-11-14",
      "2016-11-16",
      "2016-11-18",
      "2016-11-21",
      "2016-11-23",
      "2016-11-25",
      "2016-11-28",
      "2016-11-30",
      "2016-12-02",
      "2016-12-05",
      "2016-12-07",
      "2016-12-09",
      "2016-12-12",
      "2016-12-14",
      "2016-12-16",
      "2016-12-19",
      "2016-12-21"
    ]
  },
  "status":"success"
}
```
