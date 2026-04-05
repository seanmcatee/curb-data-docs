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

To fully define activities on each curb, we use a set of catchall policies:

1. A no stopping anytime policy, applied in cases where a blockface has been
marked through other means such as lane marking analysis to be a travel lane.
2. An unrestricted parking policy, applied in all cases but at a lower priority 
than the no stopping policy. 

To reserve room for further priority refinement, these catchall policies are
assigned priorities 998 and 999 respectively. Because this document addresses
policies for curbs with non-travel activities, the catchall no stopping
policy is not further explored.

## Implicit Exclusion

The CDS spec is somewhat vague on the necessity of explicit prohibitions, such
as if an allowance with limited scope implicitly prohibits users
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
    "priority": 999,
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
        "activity": "no_parking",
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
    "priority": 999,
    "rules": [{ "activity": "parking" }]
  }
]

```

For the purposes of Boston's curb inventory, we have asserted that the first
example is sufficient and that by declaring a use allowable for certain vehicle 
classes, that use is implicitly prohibited for all other vehicle classes. 
The remainder of this document relies on this assumption.
