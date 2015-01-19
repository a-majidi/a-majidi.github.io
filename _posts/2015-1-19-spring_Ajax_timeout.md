---
layout: post
title: Spring Security Ajax timeout
comments: true
---




I want to redirect users to the login page when a session timeout occurs. This works out-of-the-box with spring security, but only on non-ajax calls.
On an ajax-call you have to react on the session timeout by yourself. Therefore I have created my own filter who checks if a session is timed out.

step 1 :
add this filter :

{% highlight ruby %}


package com.majidi.filter;

import java.io.IOException;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.authentication.AuthenticationTrustResolver;
import org.springframework.security.authentication.AuthenticationTrustResolverImpl;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.web.util.ThrowableAnalyzer;
import org.springframework.security.web.util.ThrowableCauseExtractor;
import org.springframework.web.filter.GenericFilterBean;

public class AjaxTimeoutRedirectFilter extends GenericFilterBean
{

	private static final Logger logger = LoggerFactory.getLogger(AjaxTimeoutRedirectFilter.class);

	private ThrowableAnalyzer throwableAnalyzer = new DefaultThrowableAnalyzer();
	private AuthenticationTrustResolver authenticationTrustResolver = new AuthenticationTrustResolverImpl();

	private int customSessionExpiredErrorCode = 901;

	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException
	{
		try
		{
			chain.doFilter(request, response);

			logger.debug("Chain processed normally");
			//System.out.println("Chain processed normally");
		}
		catch (IOException ex)
		{
			throw ex;
		}
		catch (Exception ex)
		{
			Throwable[] causeChain = throwableAnalyzer.determineCauseChain(ex);
			RuntimeException ase = (AuthenticationException) throwableAnalyzer.getFirstThrowableOfType(AuthenticationException.class, causeChain);

			if (ase == null)
			{
				ase = (AccessDeniedException) throwableAnalyzer.getFirstThrowableOfType(AccessDeniedException.class, causeChain);
			}

			if (ase != null)
			{
				if (ase instanceof AuthenticationException)
				{
					throw ase;
				}
				else if (ase instanceof AccessDeniedException)
				{

					if (authenticationTrustResolver.isAnonymous(SecurityContextHolder.getContext().getAuthentication()))
					{
						logger.info("User session expired or not logged in yet");
						//System.out.println("User session expired or not logged in yet");
						String ajaxHeader = ((HttpServletRequest) request).getHeader("X-Requested-With");

						if ("XMLHttpRequest".equals(ajaxHeader))
						{
							logger.info("Ajax call detected, send {} error code", this.customSessionExpiredErrorCode);
							//System.out.println("Ajax call detected, send {} error code");
							HttpServletResponse resp = (HttpServletResponse) response;
							resp.sendError(this.customSessionExpiredErrorCode);
						}
						else
						{
							logger.info("Redirect to login page");
							//System.out.println("Redirect to login page");
							throw ase;
						}
					}
					else
					{
						throw ase;
					}
				}
			}

		}
	}

	private static final class DefaultThrowableAnalyzer extends ThrowableAnalyzer
	{
		/**
{% endhighlight %} 

Step 2 :

Add to spring security xml file:

{% highlight ruby %}

  <beans:bean id="ajaxTimeoutRedirectFilter" class="com.majidi.filters.AjaxTimeoutRedirectFilter">
		<beans:property name="customSessionExpiredErrorCode" value="901"/>
	</beans:bean>
	
{% endhighlight %} 

And add to http block in spring security xml file :

{% highlight ruby %}
<custom-filter ref="ajaxTimeoutRedirectFilter" after="EXCEPTION_TRANSLATION_FILTER"/>
{% endhighlight %} 


Step 3 : 
 Add this java script lines in your pages :

{% highlight ruby %}
function sessionTimeOutModal()
{

	window.location.replace("./login");
	
	
}

!function( $ )
{
	
	$.ajaxSetup({
		statusCode: 
		{
			901: sessionTimeOutModal
		}
	});
	
	
	
}(window.jQuery);
{% endhighlight %} 

Step 4 :

If you don’t use spring java configuration then web.xml is like this:



{% highlight ruby %}
<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext-security.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	
	<servlet>
		<servlet-name>AjaxSessionExpiration</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:ajaxsessionexpiration-demo-mvc.xml</param-value>
		</init-param>	
		<load-on-startup>1</load-on-startup>
	</servlet>
	
	<servlet-mapping>
	 	<servlet-name>AjaxSessionExpiration</servlet-name>
		<url-pattern>/*</url-pattern>
	</servlet-mapping>

    <filter>
        <filter-name>springSecurityFilterChain</filter-name>
        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
    </filter>
    
	<filter>
		<filter-name>characterEncodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	
	<filter-mapping>
    	<filter-name>characterEncodingFilter</filter-name>
    	<url-pattern>/pages/*</url-pattern>
	</filter-mapping>
    <filter-mapping>
        <filter-name>springSecurityFilterChain</filter-name>
        <servlet-name>AjaxSessionExpiration</servlet-name>
    </filter-mapping>

	<session-config>
		<session-timeout>1</session-timeout>
	</session-config>    				
</web-app>

{% endhighlight %} 
