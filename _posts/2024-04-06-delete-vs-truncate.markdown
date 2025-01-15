---
layout: post
title:  "Delete vs Truncate"
date:   2024-04-06 08:40:00 +0700
categories: programming
permalink: /delete-vs-truncate/
---
#### Difference between Delete vs Truncate
`DELETE` is used to remove rows from a table based on specified conditions.  
`TRUNCATE` removes all rows in a table without any conditions. It cannot be used to selectively delete specific rows.

`DELETE` logs individual row deletions in the transaction log. Each deleted row is logged, making it possible to roll back changes.  
`TRUNCATE` operation is minimally logged. It logs the deallocation of data pages but not individual row deletions, resulting in faster performance compared to `DELETE` and `TRUNCATE` can not roll back changes.

`DELETE` triggers (if defined on the table) are fired for each deleted row.  
`TRUNCATE` does not invoke any delete triggers because it doesn't log individual row deletions.

If the table has identity columns, `TRUNCATE` resets the identity seed to its original value.