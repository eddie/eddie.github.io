---
layout: post
title: Reservoir sampling in PHP
tags : [php,algorithms]
---

On a recent project I needed to take a random sample of items from a large data source that was too big to contain in memory. As the whole set of data couldn't be kept in memory, usual sampling techniques wouldn't work.

The solution to this problem is called [Reservoir Sampling](http://en.wikipedia.org/wiki/Reservoir_sampling) and allows us to choose k  samples from a list of N items in O(N) time.

<!-- more -->
To begin with we fill an array (the reservoir) with k items from our data source (items[0..k-1]). Then we iterate from the (k+1)th element to the nth element generating a random number r between 0 and the current item index. If r is in the reservoir range [0..k-1] then we replace the rth item of the reservoir with items[index].

Below is an example of reservoir sampling written in PHP, not really the correct usage as our data source is already in memory, but it's an implementation of the algorithm nevertheless. 

<h2>Reservoir Sampling example in PHP</h2>

{% highlight php %}
<?php

$dataSize = 1000;
$data = [];

// Populate data with $dataSize random integers

for($i = 0; $i < $dataSize; $i++) {
    $data[] = $i;
}

// Take sample of size $k from $data
// Samples stored in $result

$k = 10;
$result = [];

// Create reservoir

for($i = 0; $i < $k; $i++) 
  $result[$i] = $data[$i];

// Iterate from the (k+1)th to the nth element and 
// replace elements in reservoir with a decreasing probability

for(; $i < $dataSize; $i++) {

  $r = (int) rand(0, $i);
  if($r < $k) {
    $result[$r] = $data[$i];
  }
}

echo implode(' - ',$result)."\n";

{% endhighlight %}
<br>
