---
layout: post
title: how to get URL Parameters using jQuery
comments: true
---
this function returns value of any parameters variable:

 
{% highlight ruby %}
function GetURLParameter(sParam)
{
    var sPageURL = window.location.search.substring(1);
    var sURLVariables = sPageURL.split('&');
    for (var i = 0; i < sURLVariables.length; i++) 
    {
        var sParameterName = sURLVariables[i].split('=');
        if (sParameterName[0] == sParam) 
        {
            return sParameterName[1];
        }
    }
}
{% endhighlight %}
And this is how you can use this function assuming the URL is,
 "http://mysite.com/?firstparam=hello&secondparam=goodbye".
 
 {% highlight ruby %}
var firstparam = GetURLParameter('firstparam');
var secondparam = GetURLParameter('secondparam');
{% endhighlight %}

So in above code variable "firstparam" will have "hello" as value and "secondparam" variable's will be goodbye.

