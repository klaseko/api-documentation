# API Reference

## Introduction

```
API Endpoints
Live: https://api.klaseko.com
Test: https://api-test.klaseko.com

```

## Topics
- Authentication
- Errors

## Resources

#### Activities
Activities are generated whenever a `Venue`, `Program`, `VenueProgram`, or `Schedule` is `created`, `updated`, or `deleted`.

| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|
| school_id   | integer  | Foreign key                                          |
| venue_id    | integer  | Foreign key                                          |
| agent       | string   | User who performed the action.                       |
| resource    | string   | Resource that was `created` / `updated` / `deleted`. |
| identifier  | string   | Resource's identifier. Example is name of venue.     |
| action      | string   | The action performed on the resource.                |

Example Response
```json
{
  "id": 81,
  "school_id": 1218,
  "venue_id": 626,
  "agent": "Francis Borbe",
  "resource": "Venue",
  "identifier": "3202 Church Street",
  "action": "updated",
  "created_at": "Tue, 22 Nov 2016 15:00:45 PHT +08:00",
  "updated_at": "Tue, 22 Nov 2016 15:00:45 PHT +08:00"
}
```

#### Discounts
| Attribute     | Type     | Description                                      |
|---------------|----------|--------------------------------------------------|
| enrollment_id | integer  | Foreign key                                      |
| amount        | decimal  | Total discount.                                  |
| kind          | string   | Type of discount.                                |
| remarks       | text     | Additional information about the discount.       |

Example Response
```json
{
  "id": 22,
  "enrollment_id": 198,
  "amount": 250,
  "kind": "venue_program",
  "remarks": "Early Bird discount.",
  "created_at": "Wed, 23 Nov 2016 03:22:51 PHT +08:00",
  "updated_at": "Wed, 23 Nov 2016 03:22:51 PHT +08:00"
}
```

#### Enrollees
| Attribute        | Type     | Description                                    |
|------------------|----------|------------------------------------------------|
| school_id        | integer  | Foreign key                                    |
| venue_id         | integer  | Foreign key                                    |
| program_id       | integer  | Foreign key                                    |
| venue_program_id | integer  | Foreign key                                    |
| schedule_id      | integer  | Foreign key                                    |
| enrollment_id    | integer  | Foreign key                                    |
| birthdate        | date     | Enrollee's date of birth                       |
| gender           | string   | Gender.                                        |
| status           | string   | Enrollee's status.                             |
| info             | array    | An array of additional enrollee's information. |
| fees             | array    | Array of fees charged to the enrollee.         |
| total            | decimal  | Total amount of fees.                          |
| first_name       | string   | Enrollee's given name.                         |
| last_name        | string   | Enrollee's family name.                        |
| start_date       | date     | Day the enrollee will start class.             |
| days             | array    | Class days.                                    |
| dates            | array    | Class dates.                                   |

Example Response
```json
{
  "id": 12,
  "school_id": 1218,
  "venue_id": 626,
  "program_id": null,
  "venue_program_id": 305,
  "course_id": null,
  "schedule_id": 259,
  "enrollment_id": 198,
  "birthdate": "Tue, 08 Jan 1991",
  "gender": "male",
  "status": null,
  "info": null,
  "fees": {
    "venue_program": "35000.0",
    "registration_fee": 0.0
  },
  "total": 35000.0,
  "first_name": "Francis",
  "last_name": "Borbe",
  "start_date": "Mon, 21 Nov 2016",
  "days": ["mon", "tue", "wed", "thu", "fri"],
  "dates": [
    "2016-11-21",
    "2016-11-22",
    "2016-11-23",
    "2016-11-24",
    "2016-11-25",
    "2016-11-28",
    "2016-11-29",
    "2016-11-30",
    "2016-12-01",
    "2016-12-02"
  ]
}

```

#### Enrollments
| Attribute        | Type     | Description                                       |
|------------------|----------|---------------------------------------------------|
| school_id        | integer  | Foreign key                                       |
| venue_id         | integer  | Foreign key                                       |
| program_id       | integer  | Foreign key                                       |
| venue_program_id | integer  | Foreign key                                       |
| token            | string   | A hashed string based on the mobile no and email. |
| ref_no           | string   | Unique identifier used for tracking enrollments.  |
| total            | decimal  | Total amount due of the enrollment.               |
| info             | array    | Additional information based on school's custom field.|
| email            | string   | Email used in the enrollment form. |
| status           | string   | Enrollment's status based on payment. |
| fields           | array    | For deprecation. |
| mobile_no        | string   | Mobile number used in enrollment form. |
| amount_paid      | decimal  | Accumulated amount paid. |
| balance          | decimal  | Total amount due less amount paid and discount. |
| discount         | decimal  | Total discount. |
| expiration       | date     | Date of expiration. |
| slug             | string   | URL-safe ref_no. |
| fees             | array    | Array of additional fees, such as registration fee.|
| confirmed_at     | date     | Date the enrollment was completely paid. |
| payment_method   | string   | How the enrollment was settled. Either Online or on Onsite / Venue. |
| enrolled_at      | date     | Date when enrollment was created. |
| source           | string   | Where the enrollee enrolled. Either online or at the venue. |
| expired          | boolean  | `true` when no payment was made after expiration date, otherwise, `false`. |

Example Response
```json
{
  "id": 198,
  "school_id": 1218,
  "venue_id": 626,
  "program_id": null,
  "course_id": null,
  "venue_program_id": null,
  "token": "c2830a75b36b8",
  "ref_no": "1LEAV1",
  "total": 35000.00,
  "info": null,
  "email": "francis@klaseko.com",
  "status": "confirmed",
  "fields": null,
  "mobile_no": "09361000478",
  "amount_paid": 0.00,
  "balance": 35000.00,
  "discount": 0.0,
  "expiration": "Wed, 16 Nov 2016 18:21:13 PHT +08:00",
  "slug": "1leav1",
  "fees": {},
  "confirmed_at": "Wed, 16 Nov 2016 18:06:33 PHT +08:00",
  "payment_method": "venue",
  "enrolled_at": "Wed, 16 Nov 2016 18:06:13 PHT +08:00",
  "source": "venue",
  "expired": false
}

```

#### Holidays
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Payments
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Programs
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Schedules
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Schools
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Users
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### VenuePrograms
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### VenueUsers
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|

#### Venues
| Attribute   | Type     | Description                                          |
|-------------|----------|------------------------------------------------------|
