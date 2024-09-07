#  Check for the existence of a value or the state of an object


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

