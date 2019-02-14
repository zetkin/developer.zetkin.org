---
title: Smart Searches
layout: default
---

## Filters
Filters are specified as objects, and concatenated in an array which defines the
entire query. All filter objects follow the same specification, and must have
`type` and `config` attributes, along with an optional `op` attribute.

* The `op` attribute defines how this filter is concatenated with previous
  filters, i.e. how the results of this filter are added to or subtracted from
  the results of all previous filters.
* The `type` attribute specifies what type of filter this is.
* The `config` attribute contains a type-specific configuration for the filter,
  which is passed to the filter function internally.

```json
{
    "op": "sub or add",
    "type": "type_name",
    "config": {},
}
```

Below are descriptions of all implemented filters and their respective config
parameters.

### All people (`all`)
This filter just returns all the people belonging to an organization.

```json
{
    "type": "all",
    "config": null,
}
```

There are no config options. The `all` filter always returns every person in
the database.

### Person data (`person_data`)
This filter matches the specified fields against a field directly on the person
object (e.g. first name, last name or phone number.)

```json
{
    "type": "person_data",
    "config": {
        "fields": {
            "first_name": "Clara",
            "last_name": "Zetkin"
        }
    }
}
```

### Person tags (`person_tags`)
This filter maches people based on how they have been tagged.

```json
{
    "type": "person_tags",
    "config": {
        "condition": "none",
        "tags": [1, 2, 3]
    }
}
```

The `tags` property must be a non-empty array of tag IDs. An error is thrown
if the array is empty or has not been defined.

The different values for `condition` are:

* `any`, matching people who have been tagged with any of the specified tags.
* `all`, matching people who have been tagged with all of the specified tags.
* `none`, which matches people who have not been tagged with any of the
  specified tags.

### Most active (`most_active`)
This filter selects the N most active people, defined as those who participate
in the largest number of actions, within an optional time period.

```json
{
    "type": "most_active",
    "config": {
        "size": 20,
        "before": "1933-06-20",
        "after": "1857-07-05",
    }
}
```


### Campaign participation (`campaign_participation`)
This filter checks whether a person participated in the specified campaign (or
any campaign) at all, during the optional range of dates defined by the
`before` and `after` attributes.

```json
{
    "type": "campaign_participation",
    "config": {
        "operator": "in",
        "campaign": 1,
        "activity": 2,
        "location": 3,
        "state": "booked",
        "after": "1857-07-05",
        "before": "now"
    }
}
```

The operators are:

* `in` matches persons who participate in the specified campaign (or any
  campaign)
* `notin` matches persons who do not participate in the specified campaign (or
  any campaigns)

If the `campaign` attribute is not specified, the operators instead refer to
_any_ campaign, i.e. the `notin` operator when used without a specified
campaign will match all people who do not participate in any campaigns at all.

The same goes for the `activity` and `location` attributes.

The `state` attribute can be one of the following:

* `signed_up` means that only people who have signed up are selected
* `booked` (default) means that only people who have been booked are selected

The `before` and `after` attributes are dates and can be specified according to
a number of formats described in more detail in the _Date attributes_ section.

### Call history (`call_history`)

```json
{
    "type": "call_history",
    "config": {
        "operator": "called",
        "assignment": 1,
        "minTimes": 3,
        "after": "-20d"
    }
}
```

The operators are:

* `called` matches persons who were (successfully or unsuccessfully) called.
* `reached` matches persons who were called and reached, i.e. where the state
  of the call was "succesful".
* `notreached` matches persons who were not reached (but may have been tried).

If the `assignment` attribute is not specified, the search will be made against
_any_ assignment, i.e. the `reached` operator when used without a specified
assignment will match all people who have been reached in any assignment.

The `before` and `after` attributes are dates and can be specified according to
a number of formats described in more detail in the _Date attributes_ section.

The `minTimes` attribute defines a minimum number of calls matching the criteria
specified by `assignment`, `before` and `after`. This way, it's possible to find
people who have been called at least 4 times during a certain assignment, or
during the last month.

### Caller (`caller`)
```json
{
    "type": "caller",
    "config": {
        "operator": "assigned",
        "assignment": 1
    }
}
```

The operators are:

* `assigned` matches persons who are currently assigned as caller to the call
   assignment specified by `assignment`.
* `notassigned` matches persons who are not assigned as caller in the call
   assignment specified by `assignment`.

### Call blocked (`call_blocked`)
Returns people who cannot be called at this time , for any (or a specified)
reason.

```json
{
    "type": "call_blocked",
    "config": {
        "reason": "any"
    }
}
```

The `reason` attribute can have the following values:

* `allocated` will return those who are already allocated by someone else
* `organizer_action_needed` will return those who cannot be called because
  organizer action is pending.
* `call_back_after` will return those who cannot be called because a previous
  caller specified that they should not be called back until after a certain
  date which have not yet passed.
* `cooldown` will return those who have been called within the time limit
  specified for the relevant call assignment.
* `no_number` will return those who cannot be called because there is no phone
  number on file.
* `any` will return all of the above.

### Survey submissions (`survey_submission`)
The survey submission selection includes people based on whether they submitted
responses to a survey.

```json
{
    "type": "survey_submission",
    "config": {
        "operator": "submitted",
        "survey": 1,
        "after": "-20d"
    }
}
```

The operators are:

* `submitted` matches persons who submitted the survey.

The `survey` attribute is required and defines which survey the selection
should be based on. The `after` attribute is optional. If present, matching
will be restricted to responses submitted after the defined date.

### Survey responses (`survey_response`)
The survey response selection includes people based on a free text search of
responses to survey questions.

```json
{
    "type": "survey_response",
    "config": {
        "operator": "in",
        "survey": 2,
        "value": "foobar"
    }
}
```

The operators are (all operators are _case insensitive_):

* `eq` requires that the response is exactly `value`.
* `noteq` requires that the response is not exactly `value`.
* `in` yields true for responses that contain `value`.
* `notin` yields false for responses that contain `value`.

When `survey` is specified, all responses in that survey will be searched.
Replace the `survey` attribute with the `question` attribute, the value of which
is the ID of the question, to limit the matching to a single question.

### Survey options (`survey_option`)
The survey option selection includes people based on options they've selected
(or not) in response to survey questions.

```json
{
    "type": "survey_option",
    "config": {
        "operator": "all",
        "question": 2,
        "options": [1, 2]
    }
}
```

The operators are:

* `any` yields true for survey submissions where any of the defined `options`
  have been selected in response to the defined question.
* `all` yields true for survey submissions only where all of the defined
  `options` have been selected in response to the defined question.
* `none` yields true for survey responses where none of the defined `options`
  have been selected in response to defined question.

  The `question` attribute must be defined. The `options` attribute must always
  be defined as an array, even if there is only one option selected.

### Aggregate sub-query (`sub_query`)
The sub-query filter allows for aggregating several queries into one, using the
standard operators when concatenating their results. This is different from
concatenating the filters of each individual query linearly, because with
sub-queries, the add/subtract operations are first evaluated in the sub-query,
and then the total sum of each sub-query are concatenated.

This allows for constructions like _f0 - (f1 + f2)_ where the results of the
query consisting of filters f1 and f2 will be subtracted from filter f0, while
a flat concatenation of _f0 - f1 + f2_ would result in f1 being subtracted
from f0 before finally adding the results of f2.

```json
{
    "type": "sub_query",
    "config": {
        "query_id": 1
    }
}
```

The query referenced by `query_id` can in turn contain other sub-query filters,
as long as there are no circular dependencies. If queryId is not defined,
references a query that does not exist, or results in direct or in-direct
circular dependencies, an error will be returned.

### Random (`random`)
The random filter selects a random subset of the people in an organization. The
size of the subset is defined in the config.

```json
{
    "type": "random",
    "config": {
        "size": 0.7,
        "seed": "abc123"
    }
}
```

The `size` property defines how many people to return and must be a positive
number. Integers above 1 are interpreted as a fixed size, meaning that a value
of 5 will result in at most 5 persons being returned. Fractions between 0 and 1
are interpreted as a percentage of the total number of people in the
organization, so a value of 0.5 will return 50% of the people.

The `seed` property can be used to refresh the random selection. The selection
is stable in the sense that given the same seed, the same subset will always be
returned, as long as the database doesn't change.

If the seed changes, the subset will be very different. If the database changes,
e.g. by adding or removing people, the random subset will at most change by the
number of people added and removed.

The default seed is the empty string.

### Zetkin users (`user`)
The `user` filter finds person objects based on whether they are connected to a
Zetkin user.

```json
{
    "type": "user",
    "config": {
        "is_user": true
    }
}
```

The only configuration attribute is `is_user`. Setting this to `true` will find
people who are connected to a user. Setting it to `false` will find people who
are not.

## Date attributes
Many filters have attributes that define dates, e.g. the `before` and `after`
attributes. Dates can be specified using a number of formats.

### Static dates
Static dates, i.e. dates defined upon saving the query, are specified using
`yyyy-mm-dd` notation. Using a static date means that the same date will be
used for all future executions of the query.

### Dynamic dates
There are two types of dynamic date definitions; the `now` keyword, and date
offsets. Dynamic dates are evaluated when the query executes, meaning that
filters which have been configured with dynamic date attributes may match
different people from one day to the next.

#### The current date (`now`)
The special `now` keyword will be evaluated to the current date upon query
execution.

#### Date offsets
Date offsets are similar to now (and internally rely on the same `NOW()` SQL
function) but actually evaluate to date relative to the current date. Date
offsets are defined using the syntax `<op><scalar><unit>` where:

* `op` can be either `+` or `-` and will add or subtract to the current date
  respectively.
* `scalar` is a number that defines the size of the offset.
* `unit` defines the unit for the offset. At this time, days is the only
  support unit, denoted by `d`.

Examples:

* `-10d` is "ten days ago".
* `+30d` is "30 days from now".
* `+0d` (or `-0d`) is zero days from now, i.e. today, the same as `now`.

## Filter concatenation
Filters are concatenated differently depending on the value of the `op`
attribute of the next filter. The two options are `add` (default) or `sub`.

### Extending (add)
The `add` operator means that the results of all previous filters are extended
by the results of the next filter. If all previous filters result in 100 matches
and the next filter alone matches 20 matches, the result will be up to 120
matches. It may result in fewer matches if there are duplicate results between
the two. In SQL terms, this operation is a simple `OR`.

The `add` operator is the default which is used if no operator is defined in
the filter specification.

### Narrowing (sub)
The `sub` operator means that the results of the next filter is removed from the
results of all previous filters. If all previous filters result in 100 matches
and the next filter alone matches 20 matches, the aggregate result will between
80-100 matches. The exact number depends on how many of the 20 were already in
the 100. In SQL terms, this operations is a `AND NOT`.
