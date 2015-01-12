---
layout: post
title: Concurrency control in Custom ProviderManager(Spring Security)
comments: true
---

to stop the second same user to login add these lines to spring_security.xml

{% highlight ruby %} 
<session-management>
  <concurrency-control error-if-maximum-exceeded="true" max-sessions="1"/>
</session-management>

{% endhighlight %} 

Step1:

put

{% highlight ruby %} 
<listener>
 <listener-class>org.springframework.security.web.session.HttpSessionEventPublisher</listener-class>
</listener>
{% endhighlight %} 
in web.xml

Step2:

define my authentication provider "securityAuthenticationProvider" which extends of DaoAuthenticationProvider

{% highlight ruby %} 
<global-method-security pre-post-annotations="enabled" />

    <http use-expressions="true" auto-config="true">
        <intercept-url pattern="/my/**" access="hasAnyRole('USER','LENDER','BORROWER')" />

				<!--Lender-->				
				<intercept-url pattern="/lend/beLenderPage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/lend/autoBid.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/lend/favoritePage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/lend/userHomePage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/lend/bidPageAction.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<!--Borrower-->			
				<intercept-url pattern="/borrow/beBorrowerPage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/borrow/getMoreCreditPage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/borrow/borrowPage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/borrow/tryBorrowPage.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				<intercept-url pattern="/borrow/bidPageAction.action" 
				access="hasAnyRole('USER','LENDER','BORROWER')" />
				
        <intercept-url pattern="/**" access="permitAll" />
        <!--<http-basic />-->
        <form-login login-page="/loginPage.action?error=false"
        	authentication-failure-url="/loginPage.action?error=true" default-target-url="/"/>
        <logout />
        
        <session-management>
    			<concurrency-control error-if-maximum-exceeded="true" max-sessions="1"/>
  			</session-management>

    </http>

    <authentication-manager alias="authenticationManager">
        <authentication-provider ref="securityAuthenticationProvider" />
    </authentication-manager>
		
    <beans:bean id="securityUserService" class="com.renrendai.security.SecurityUserService"/>

 
	
    <beans:bean id="passwordEncoder" class="org.springframework.security.authentication.encoding.Md5PasswordEncoder"/>
    
{% endhighlight %} 

step4:
    
    if you use customized principal object , check UserDetails implementation and add these methods :
    
    
    
{% highlight ruby %} 
    @Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((user == null) ? 0 : user.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		SecurityUser other = (SecurityUser) obj;
		if (user == null) {
			if (other.user != null)
				return false;
		} else if (!user.equals(other.user))
			return false;
		return true;
	}
{% endhighlight %} 	
	
	
	full code :
	
	
{% highlight ruby %} 

	public class SecurityUser implements UserDetails {

    private static final long serialVersionUID = -4294694251587167808L;

    public int getUserId() {
        return user.getUserId();
    }
    private User user;

    public SecurityUser(User user) {
        this.user = user;
    }

    @Override
    public Collection<GrantedAuthority> getAuthorities() {
        List<GrantedAuthority> list = new LinkedList<GrantedAuthority>();
        for (UserRole r : user.getRoles()) {
            list.add(new GrantedAuthorityImpl(r.name()));
        }

        return list;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUsername();
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return user.isEnabled();
    }

    public User getUser() {
        return user;
    }
    
    public void setUser(User user){
    	this.user = user;
    }

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((user == null) ? 0 : user.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		SecurityUser other = (SecurityUser) obj;
		if (user == null) {
			if (other.user != null)
				return false;
		} else if (!user.equals(other.user))
			return false;
		return true;
	}
}


{% endhighlight %} 

