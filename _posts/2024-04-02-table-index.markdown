---
layout: post
title:  "Table Index"
date:   2024-04-02 08:40:00 +0700
categories: programming
permalink: /table-index/
---
`Index` is a data structure (like dictionary) that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space. Indexing involves creating an organized structure that allows the database management system (DBMS) to locate and access rows more efficiently based on the values in one or more columns.

Types of Indexes:

- `Single Column Index`: Created on a single column.
- `Composite Index`: Created on multiple columns.
- `Unique Index`: Ensures that the indexed columns contain unique values.
- `Clustered Index`: Determines the physical order of data rows in the table.
- `Non-Clustered Index`: Creates a separate structure for the index, keeping the data rows separate from the index structure.

#### Clustered index vs Non-clustered index
The `clustered index` should be for a key column, meaning it can not have repeated values. Primary Keys of the table by default are clustered indexes.  
`Non-clustered index` for other columns and it's stored separately (like a dictionary), then you can have multiple non-clustered indexes in a table. The composite key when used with unique constraints of the table act as the non-clustered index. So the clustered index is faster.