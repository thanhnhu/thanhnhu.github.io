---
layout: post
title:  "Table vs View"
date:   2024-02-03 08:40:00 +0700
categories: programming
permalink: /table-vs-view/
---
A table contains data, a view is just a SELECT statement which has been saved in the database (more or less, depending on your database).

The advantage of a view is that it can join data from several tables thus creating a new view of it. Say you have a database with salaries and you need to do some complex statistical queries on it.  
Instead of sending the complex query to the database all the time, you can save the query as a view and then SELECT * FROM view

Modification through a view (e.g. insert, update, delete) is not permitted (In SQL Server, you can modify the underlying table through a view, if it only references one base table)