---
layout: post
title: Builder Pattern
---

<h1>Builder Pattern</h1>

<p>I use builder pattern quite a lot. Have a search query you want to build? Object that has plenty of fields with some of them mandatory? Builder pattern is here to save you!</p>
  
<p>The idea of the builder pattern is to gradually construct an object without using the object's constructor directly, but with a separate builder object. By using the builder object we can avoid having numerous constructors for an object. We also can make sure that the created object is in a consistent state. Let's see how this works by an example.</p>

<h2>Use Case</h2>

<p>I need an employee object that contains basic information about the employee. The employee has fields both <b>mandatory</b> and <b>optional</b>. How do I make sure that my employee class is <b>always</b> in a consistent state with all necessary fields populated?</p>

<p>The following code contains a sample of the "Employee" class. The object has private fields with setters and getters provided.</p>

<h3>Employee.java</h3>

{% highlight java %}
public class Employee {

    private String firstName;           // Required
    private String lastName;            // Required
    private BigDecimal salaryPerMonth;  // Required
    private String jobTitle;            // Optional
    
    // getters and setters
    
{% endhighlight %}

<p>If we were to handle the employee object, we would first need to check that it is in a consistent state. So we would always do the following checks when handling an employee object</p>

{% highlight java %}
if(employee.getFirstName() != null) {
  ...
}
{% endhighlight %}

<h2>Solution</h2>
  
<p>There is an easier way! Check out the new improved Employee.java</p>

{% highlight java %}

public class Employee {

    private String firstName;           // Required
    private String lastName;            // Required
    private BigDecimal salaryPerMonth;  // Required
    private String jobTitle;            // Optional

    private Employee(EmployeeBuilder employeeBuilder) {
        this.firstName = employeeBuilder.firstName;
        this.lastName = employeeBuilder.lastName;
        this.salaryPerMonth = employeeBuilder.salaryPerMonth;
        this.jobTitle = employeeBuilder.jobTitle;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public BigDecimal getSalaryPerMonth() {
        return salaryPerMonth;
    }

    public String getJobTitle() {
        return jobTitle;
    }

    public static class EmployeeBuilder {
        private String firstName;
        private String lastName;
        private BigDecimal salaryPerMonth;
        private String jobTitle;

        public EmployeeBuilder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public EmployeeBuilder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public EmployeeBuilder salaryPerMonth(BigDecimal salaryPerMonth) {
            this.salaryPerMonth = salaryPerMonth;
            return this;
        }

        public EmployeeBuilder jobTitle(String jobTitle) {
            this.jobTitle = jobTitle;
            return this;
        }

        public Employee build() {

            Employee employee = new Employee(this);

            if (employee.getFirstName() == null) {
                throw new IllegalStateException("First name is null!");
            }

            if (employee.getLastName() == null) {
                throw new IllegalStateException("Last name is null!");
            }

            if (employee.getSalaryPerMonth() == null) {
                throw new IllegalStateException("Salary per month is null!");
            }

            return employee;
        }
    }
}

{% endhighlight %}

<p>What have we done? Well, we've used a builder object "EmployeeBuilder" that will construct an immutable "Employee" object with all mandatory fields guaranteed to be populated. The cool thing about this pattern is also the way you construct new objects.</p>

{% highlight java %}

Employee employee = new Employee.EmployeeBuilder()
        .firstName("Sebastian")
        .lastName("Monte")
        .jobTitle("Developer")
        .salaryPerMonth(new BigDecimal(0))
        .build();
        
{% endhighlight %}

<p>If you miss a mandatory field, the object will not be created and you'll be greeted with an "IllegalStateException" :)</p>