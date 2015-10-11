---
layout: post
title: JAX-WS + Spring
comments: true
---
<script src="https://gist.github.com/a-majidi/071ab1a6dbef929cdcfa.js"></script>
<span style="float:right;direction:rtl;">
 برای پیاده ساز ی یک وب سرویس در فریم ورک spring می توان به دو روش عمل کرد. یک روش استفاده از spring ws است که با استفاده از یک فایل xml با پسوند .xsd ورودی وب سرویس تعریف می شود. از آنجایی که من معمولا علاقه ای به استفاده از xml ندارم و از آنجایی که استفاده از jax-ws بسیار ساده تر است از این روش برای تعریف وب سرویس در این پست استفاده شده است.
قدم اول:
خطوط زیر را به فایل pom خود اضافه نمایید. در صورت عدم استفاده از maven باید چند فایل jar به پروژه اضافه کنید. Library های مورد نیاز : jaxws-rt و jaxws-spring
Pom.xml:
 
</span>

{% gist 071ab1a6dbef929cdcfa %}

<span style="float:right;direction:rtl;">
</span>
