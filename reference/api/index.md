---
title: API reference
layout: default
---
This is a reference for the Zetkin Platform API.


## General information

###  Pagination

Collections can be paginated by supplying paging parameters to the request.

- p: Page. The requested page index.
- pp: Per page. Number of items per page.

```json
{
	"p": "any non negative number",
	"pp": "any non negative number",
}
```
