---
icon: lucide/user-round-pen
---

# CDS Adaptations and Implementation Notes

Some elements of the CDS API spec require a bit of interpretation, or some 
explicit decisions that may vary from implementation to implementation. 
Such decisions are documented here.

## Catchall Policy

The Boston CDS implementation describes allowable activities (e.g., parking)
in a curb zone for various user classes (e.g., passenger cars or commercial vehicles).
This is accomplished through automated
interpretation of sign images and conversion of these images to one or more
**policy** objects that represent rules on a sign.

To fully define activities on each curb, we use a pair of catchall policies:

1. A no stopping anytime policy, applied in cases where a blockface has been
marked through other means such as lane marking analysis to be a travel lane.
2. An unrestricted parking policy, applied in all cases but at a lower priority 
than the no stopping policy. 

These policies are prioritized in a manner consistent with the [prioritization 
approach](priorities.md). Of note, the unrestricted parking policy is assigned a specific 
priority value of 99.

## Implicit Exclusion

The CDS spec is unclear on the necessity of explicit prohibitions, such
as if an allowance with limited scope implicitly prohibits users and activities
outside of that scope from using a curb zone. For example, a
policy that allows 30-minute commercial vehicle parking only between 6:00 and 10:00 am
and allows unrestricted parking at other times might be
represented by the example below:

```json
[
  {
    "name": "Policy 1 - allows limited commercial vehicle parking",
    "priority": 1,
    "rules": [
      {
        "activity": "parking",
        "max_stay": 30,
        "max_stay_unit": "minute",
        "user_classes": ["commercial_vehicle"]
      }
    ],
    "time_spans": [
      {
        "time_of_day_start": "06:00",
        "time_of_day_end": "10:00"
      }
    ]
  },
  {
    "name": "Unrestricted parking",
    "priority": 99,
    "rules": [
      {
        "activity": "parking"
      }
    ]
  }
]
```

Alternately, the approach below could be necessary to explicitly prohibit
parking by non-commercial vehicles during the specified timeframe.

```json
[
  {
    "name": "Policy 1 - allows limited commercial vehicle parking and prohibits other vehicles from parking",
    "priority": 1,
    "rules": [
      {
        "activity": "parking",
        "max_stay": 30,
        "max_stay_unit": "minute",
        "user_classes": ["commercial_vehicle"]
      },
      {
        "activity": "no parking",
        "user_classes_except": ["commercial_vehicle"]
      }
    ],
    "time_spans": [
      {
        "time_of_day_start": "06:00",
        "time_of_day_end": "10:00"
      }
    ]
  },
  {
    "name": "Unrestricted parking",
    "priority": 99,
    "rules": [{ "activity": "parking" }]
  }
]

```

For the purposes of Boston's curb inventory, we have asserted that the first
example is sufficient and that by:

1. Declaring a use allowable for certain vehicle classes, that use is implicitly prohibited 
for all other vehicle classes, and 
2. Declaring a use allowable for all vehicle classes, other uses are implicitly prohibited for 
all vehicle classes.

!!! info "Definitions"
    In the assumptions above, **use** means any activity, purpose, or combination thereof. 

    Activities follow the hierarchy defined in CDS. For example, the `parking` activity 
    includes allowance of stopping and loading.

The remainder of this document relies on these assumptions.

## Enumerated Values

CDS includes well known values for User Classes and for Purposes. Our implementation
is deliberately limited to the values described below.

### User Classes

CDS defines `user_class` field as using the **AND** operator. We've created
a set of values different from the well known values included in the CDS
specification that is more applicable to Boston parking regulations
encountered in this process. This list includes:

- Vehicle Types
    - bicycle
    - bus
    - car
    - motorcycle
    - truck
- Vehicle Properties
    - accessible
    - electric

- Additional Values (not well known values listed in CDS)
    - commercial_vehicle
    - resident_permit
    - rideshare
    - valet


Additional `user_class` values may be added as they are encountered in the
dataset.


### Purposes

This project is minimizing the `purpose` field, which is specified as
using an **OR** operator. The values of purpose encountered so far include:

- emergency_use
- pickup_dropoff (not a CDS well known value)

Examples of use cases where `purpose` is important include:

- School zone pick-up and drop-off only, usually time-limited.
- Zones reserved for emergency ambulance use.

Purpose is not used to describe allowable activities that are the reason for a
no-parking regulation, such as street sweeping or temporary construction access.

Additional `purpose` values may be added as they are encountered in the
dataset.