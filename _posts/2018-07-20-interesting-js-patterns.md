---
layout: post
title: Interesting JS Patterns that I discovered
---

# Array Destructuring Patterns #


{% codeblock Filtering Array Data lang:javascript URL link_text %}
> result = [ [null, 1],["error", null],[null, 2],["error",4] ]
[ [ null, 1 ], [ 'error', null ], [ null, 2 ], [ 'error', 4 ] ]
> result.filter(([err,data]) => err === null )
[ [ null, 1 ], [ null, 2 ] ]
{% endcodeblock %}

We can completely ignore the second value in the Array as we are interested only
on error and rewrite like this.

{% codeblock %}
> result.filter(([err]) => err === null)
[ [ null, 1 ], [ null, 2 ] ]
{% endcodeblock %}

Or as some languages do put a variable which signifies I dont care

{% codeblock %}
> result.filter(([err, __]) => err === null)
[ [ null, 1 ], [ null, 2 ] ]
{% endcodeblock %}





