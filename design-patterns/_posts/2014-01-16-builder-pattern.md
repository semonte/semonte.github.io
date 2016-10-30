---
layout: post
title: Builder Pattern
tags: [creational]
---

<h1>Builder Pattern</h1>

<p><a href="https://en.wikipedia.org/wiki/Builder_pattern">Wikipedia</a> describes builder pattern as following</p>

<p><i>The intent of the Builder design pattern is to separate the construction of a complex object from its representation. By doing so the same construction process can create different representations.</i></p>

<h2>Use Case</h2>

<p>I need an employee object that contains basic information about the employee. The employee has fields both <b>mandatory</b> and <b>optional</b>. How do I make sure that my employee class is <b>always</b> in a consistent state with all necessary fields populated?</p>

<p>The following code contains a sample of the <i>Employee</i> class. The object has private fields with setters and getters provided.</p>

~~~~~~~~

public class Employee {

    private String firstName;           // Required
    private String lastName;            // Required
    private BigDecimal salaryPerMonth;  // Required
    private String jobTitle;            // Optional
    
    // getters and setters
    
~~~~~~~~

<p>If we were to handle the employee object, we would first need to check that it is in a consistent state. So we would always do the following checks when handling an employee object</p>

~~~~~~~~

if(employee.getFirstName() != null) {
  ...
}

~~~~~~~~

<h2>Solution</h2>
  
<p>There is an easier way! Check out the new improved <i>Employee</i></p>

~~~~~~~~

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

~~~~~~~~

<p>What have we done? Well, we've used a builder object <i>EmployeeBuilder</i> that will construct an immutable <i>Employee</i> object with all mandatory fields guaranteed to be populated. The cool thing about this pattern is also the way you construct new objects.</p>

~~~~~~~~

Employee employee = new Employee.EmployeeBuilder()
        .firstName("Sebastian")
        .lastName("Monte")
        .jobTitle("Developer")
        .salaryPerMonth(new BigDecimal(0))
        .build();
        
~~~~~~~~

<p>If you miss a mandatory field, the object will not be created and you'll be greeted with an <i>IllegalStateException</i> :)</p>

<h2>Real World Example</h2>

<p>Apache's <a href="https://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/builder/ToStringBuilder.html">ToStringBuilder</a> is an example of a builder pattern. It helps to implement a <i>toString()</i> method without complex String concatenation. An example usage:</p>

~~~~~~~~

public String toString() {
    return new ToStringBuilder(this).
      append("name", name).
      append("age", age).
      build();
}

~~~~~~~~