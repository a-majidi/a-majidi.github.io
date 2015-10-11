---
layout: post
title: JAX-WS + Spring
comments: true
---

<span style="float:right;direction:rtl;font-family:nazanin,tahoma">
 برای پیاده ساز ی یک وب سرویس در فریم ورک spring می توان به دو روش عمل کرد. یک روش استفاده از spring ws است که با استفاده از یک فایل xml با پسوند .xsd ورودی وب سرویس تعریف می شود. از آنجایی که من معمولا علاقه ای به استفاده از xml ندارم و از آنجایی که استفاده از jax-ws بسیار ساده تر است از این روش برای تعریف وب سرویس در این پست استفاده شده است.
قدم اول:
خطوط زیر را به فایل pom خود اضافه نمایید. در صورت عدم استفاده از maven باید چند فایل jar به پروژه اضافه کنید. Library های مورد نیاز : jaxws-rt و jaxws-spring
Pom.xml:
 
<script src="https://gist.github.com/a-majidi/071ab1a6dbef929cdcfa.js"></script>
<!--{% gist 071ab1a6dbef929cdcfa %}-->

 فایل ws-context.xml را در فولدر resources قرار دهید:
<script src="https://gist.github.com/a-majidi/d2aab54b34c812dd819f.js"></script>

کلاس HelloWorldWS که به عنوان یک spring bean معرفی می شود. به @Component توجه کنید. نام helloWs دقیقا در ws-context.xml استفاده می شود در صورت تغییر آن فایل نیز تغییر دهید.

<script src="https://gist.github.com/a-majidi/7bfe25f5e7b0e2e5fa07.js"></script>
و کلاس HelloWorldImpl
<script src="https://gist.github.com/a-majidi/f1a1dd026da50ef330ec.js"></script>
در web.xml خطوط زیر را اضافه نمایید:

<script src="https://gist.github.com/a-majidi/ebe53987d1334559486f.js"></script>

<div style="background-color:#F5F5F5">
نکته : در صورتی که اجرای این مثال در tomcat با مشکل مواجه شد از glassfish  یا application server  های مشابه استفاده نمایید.
</div>



</span>
