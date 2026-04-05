---
icon: lucide/arrow-down-up
---

# Policy Prioritization

The examples shown above are useful in understanding the order in which 
policies must be prioritized to achieve correct representation of parking 
regulations. To allow for potential expansion and refinement, we have 
categorized policies into blocks of 10 to leave some space for expansion
and adjustment.


- 1-9: **Blanket Prohibitions** include all blanket prohibitions of activities, including 
`no_stopping`, `no_loading`, and `no_parking` prioritized as `1`, `2`, and 
`3`, respectively. Blanket prohibitions apply to all user classes, purposes, and time spans.

- 10-19: **Temporary Allowances** include regulations with a start and stop date. 
- 20-29: **Temporary Prohibitions** include regulations with a start and stop date. 

- 30-39: **Time-specific General Prohibitions** that apply to all users such as Snow Emergency, or days and times 
when parking is prohibited to allow street cleaning.

- 40-49: **User- and time-specific allowances** that target one or more specific `user_class` or 
`purpose`  and that are also only in effect for a specified time_span.

- 50-59: **User- and time-specific prohibitions** that target one or more specific `user_class` or 
`purpose`  and that are also only in effect for a specified time_span.

- 60-69: **User-specific allowances** that target one or more user_class or purpose but are 
not limited to a specific timespan.

- 60-69: **User-specific prohibitions** that target one or more user_class or purpose but are
not limited to a specific timespan.

- 70-79: **Time-specific General Allowances** that are in effect only for a specific time, but 
that do not target a specific user_class or purpose.

- 80-89: Blanket allowances that apply to all users during all timespans. (excludes 
catchall unrestricted parking.)

- 99: Catchall unrestricted parking allowance that allows parking for all vehicles and time 
spans, and that does not include any restrictions such as max_stay.


!!! warning "Temporary Parking"
    Temporary parking regulations were not a focus for this study, so prioritization 
    has not been fully explored.

!!! warning "Implicit Exclusions"
    Use of implicit exclusion may result in few or no instances of time- and/or 
    user-specific policies, and interactions between allowances and prohibitions 
    has not been tested without the implicit exclusion assumption.