```dataview

table WITHOUT ID link(file.path, file.frontmatter.title) as "Title", file.cday as "Creation Date"
from "content/posts"
sort file.cday desc
limit 10


```
