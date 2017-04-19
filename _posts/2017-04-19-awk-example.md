---
title: Awk example collections
updated: 2017-04-19 18:00
---

##### Get the last field 

`NF` refers to te number of fields of the current record, printing `$NF` means printing the last one

```
awk -F "/" '{print $NF}' input
```

##### Show the rest of a line

Sometimes, the field of each line is not the same. And if we only need to use the first serval fields, and the rest of the line will just keep the origin format and output it. so we can do like below

```
awk '{$1=$2=""; print $0}' file
```

Here we just skip first and second field, and print the rest of fields.





