---
docsection: views
layout: api_section
title: "Person views"
---

You can create "Views" through which personal information can be retrieved.
The view defines a set of columns representing different personal information
that can be stored in the view or retrieved from other sources within Zetkin.

Rows can be added to the view, or the view can be used to format lists of
people retrieved elsewhere using the `?view_id` URL parameter. For example,
the list of matches to the Smart Search query with ID 2 can be formatted
according to the columns in view 3, by fetching it from
`/orgs/1/people/queries/2/matches?view_id=3`.
