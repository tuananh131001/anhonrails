---
layout: single
title:  "Personal Ruby On Rails cheatsheet"
date:   2024-09-07 10:18:59 +0700
categories: rails
---
#  


My quick cheatsheet:

| Methods     | present? - | exists? | any?<br>+ | blank?<br>- | empty?<br>+ | none? | one?/many? | size<br>+ | count<br>- | length |
| ----------- | ---------- | ------- | --------- | ----------- | ----------- | ----- | ---------- | --------- | ---------- | ------ |
| DB hit      | X          | X       |           |             |             |       |            |           | X          |        |
| Memory Load | X          |         |           | X           |             | X     |            |           |            | X      |
| Smart       |            |         | X         |             | X           |       | X          | X         |            |        |

+: recommended
-: do not use

Note:
`one?` `many?` : using size

