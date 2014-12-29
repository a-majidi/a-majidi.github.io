@Where in hibernate can be applied at entity level. With the help of @Where, we can use where class to fetch the data. The entity will be populated only for those data for which @Where clause returns true. Find the example below. Find the example.


{% highlight ruby %} 


import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;
import org.hibernate.annotations.Where;

@Entity
@Table(name = "state")
@Where(clause="id>1") 
public class State  {
	
	@Id
	@Column(name = "id")
	private int id;
	@Column(name = "name") 
	private String name;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
}

{% endhighlight %} 


another example :


{% highlight ruby %} 

import org.hibernate.annotations.Where;
import javax.persistence.*;
 
@Entity
@Table
@Where(clause = "isDeleted='false'")
public class Customer {
 
    @Id
    @GeneratedValue
    @Column
    private Integer id;
 
    @Column
    private String name;
 
    @Column
    private Boolean isDeleted;
 
    //Getters and setters
}

{% endhighlight %} 

is equivalent to 

{% highlight ruby %}
em.createQuery("select c from Customer c where isDeleted=true").getResultSet();
{% endhighlight %} 
