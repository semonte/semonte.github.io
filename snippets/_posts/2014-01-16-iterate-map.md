---
layout: post
title: Iterate Map
---

<h1>Iterate Map</h1>
	
<p>Super easy way to iterate through a map!</p>

{% highlight java %}
for (Map.Entry<String, String> entry : strings.entrySet()) {
  System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
}
{% endhighlight %}