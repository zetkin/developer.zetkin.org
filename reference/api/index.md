---
title: API reference
layout: default
---
This is a reference for the Zetkin Platform API.


## General information

###  Pagination

Collections can be paginated by supplying paging parameters to the query string.
For example, if you want to paginate the [people collection](people), make a request
to `/orgs/1/people?p=2&pp=20`, where

- p: Page. The requested page index.
- pp: Per page. Number of items per page.


