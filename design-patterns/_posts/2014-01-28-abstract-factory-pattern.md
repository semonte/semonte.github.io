---
layout: post
title: Abstract Factory Pattern
tags: [creational]
---

<h1>Abstract Factory Pattern</h1>

<p><a href="http://en.wikipedia.org/wiki/Abstract_factory_pattern">Wikipedia</a> describes abstract factory pattern as following</p>

<p><i>The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes.</i></p>
  
<h2>Use Case</h2>

<p>I have a user service that needs to get users either from database or cache. The service method is the following.</p>

~~~~~~~~

public User getUserByName(RepositoryType repositoryType, String name)

~~~~~~~~

~~~~~~~~

public class User {

    public String name;

    public User(String name) {
        this.name = name;
    }
}

~~~~~~~~

<p>The <i>RepositoryType</i> is an enum, which specifies what repository should be used (database or cache).</p>
  
~~~~~~~~

public enum RepositoryType {
    DATABASE, CACHE
}

~~~~~~~~

<p>How can I isolate the logic of selecting the correct repository, so that the user service does not need to bother itself with such low level details. What can we do?</p>

<h2>Solution</h2>

<p>We'll start by creating an interface for finding the user by name. We do not worry about selecting the correct repository yet.</p>

~~~~~~~~

public interface IUserRepository {
    public User getUserByName(String name);
}

~~~~~~~~

<p>Next we provide two implementations for this interface (database and cached implementations).</p>

~~~~~~~~

public class DatabaseUserRepository implements IUserRepository {

    public User getUserByName(String name) {
        return new User("I am a user from database!");
    }
}

~~~~~~~~

~~~~~~~~

public class CacheUserRepository implements IUserRepository {

    public User getUserByName(String name) {
        return new User("I am a user from cache!");
    }
}

~~~~~~~~

<p>We are almost done. Now we just need to select the correct repository based on the <i>RepositoryType</i> parameter of the user service call. Easy, let's just do the following.</p>

~~~~~~~~

public class UserService {

    private IUserRepositoryFactory userRepositoryFactory;

    public UserService(IUserRepositoryFactory userRepositoryFactory) {
        this.userRepositoryFactory = userRepositoryFactory;
    }

    public User getUserByName(RepositoryType repositoryType, String name) {
        return this.userRepositoryFactory.create(repositoryType).getUserByName(name);
    }
}

~~~~~~~~

<p>Where did that <i>IUserRepositoryFactory</i> come from and what does it do? Hmm...</p>

~~~~~~~~

public interface IUserRepositoryFactory {
    public IUserRepository create(RepositoryType repositoryType);
}

~~~~~~~~

~~~~~~~~

public class UserRepositoryFactory implements IUserRepositoryFactory {

    private IUserRepository databaseUserRepository;
    private IUserRepository cacheUserRepository;

    public UserRepositoryFactory(IUserRepository databaseUserRepository,
                                 IUserRepository cacheUserRepository) {
        this.databaseUserRepository = databaseUserRepository;
        this.cacheUserRepository = cacheUserRepository;
    }

    public IUserRepository create(RepositoryType repositoryType) {

        if (RepositoryType.DATABASE.equals(repositoryType)) {
            return databaseUserRepository;
        } else if (RepositoryType.CACHE.equals(repositoryType)) {
            return cacheUserRepository;
        }

        throw new IllegalArgumentException("RepositoryType not supported!");
    }
}

~~~~~~~~

<p>As you can see, <i>IUserRepositoryFactory</i> is an interface which is implemented by <i>UserRepositoryFactory</i>. The <i>UserRepositoryFactory</i> is responsible for the logic of selecting the correct repository. The user service is completely unaware of this logic and we could easily add more repositories just be modifying the <i>UserRepositoryFactory</i> class!</p>

<p>Here is an example of how the service is used. <b>Note: the first four lines would actually be handled by a dependency injection framework.</b></p>
~~~~~~~~

IUserRepository databaseUserRepository = new DatabaseUserRepository();
IUserRepository cacheUserRepository = new CacheUserRepository();
IUserRepositoryFactory userRepositoryFactory = new UserRepositoryFactory(databaseUserRepository, cacheUserRepository);
UserService userService = new UserService(userRepositoryFactory);

User userFromDatabase = userService.getUserByName(RepositoryType.DATABASE, "");
User userFromCache = userService.getUserByName(RepositoryType.CACHE, "");

assertEquals("I am a user from database!", userFromDatabase.name);
assertEquals("I am a user from cache!", userFromCache.name);

~~~~~~~~

<p>I hope that <a href="http://en.wikipedia.org/wiki/Abstract_factory_pattern">Wikipedia's</a> first line about abstract factories makes even a bit more sense to you than before :)</p>

<h2>Real World Example</h2>

<p>Java's <i><a href="http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilderFactory.html">javax.xml.parsers.DocumentBuilderFactory</a></i> is an example of an abstract factory pattern. The <i><a href="http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newInstance--">newInstance()</a></i> method returns a <i>DocumentBuilderFactory</i> implementation based on the runtime parameters that vary with different applications. The <i>DocumentBuilderFacory</i> can be compared to our <i>UserRepositoryFactory</i> class: it also returns an implementation based on a given parameter.</p>

<p>Once a <i>DocumentBuilderFactory</i> is initialized with the <i>newInstance()</i> method, the <a href="http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilderFactory.html#newDocumentBuilder--"><i>newDocumentBuilder()</i></a> method can be invoked. This method returns an implementation of a <a href="http://docs.oracle.com/javase/8/docs/api/javax/xml/parsers/DocumentBuilder.html"><i>DocumentBuilder</i></a>. It can be, for example, <a href="https://xerces.apache.org/xerces-j/apiDocs/org/apache/xerces/jaxp/DocumentBuilderImpl.html"><i>org.apache.xerces.jaxp.DocumentBuilderImpl</i></a> or <a href="http://www.saxonica.com/html/documentation/javadoc/net/sf/saxon/dom/DocumentBuilderImpl.html"><i>net.sf.saxon.dom.DocumentBuilderImpl</i></a> classes. These implementations are comparable to our <i>DatabaseUserRepository</i> and <i>CacheUserRepository</i> classes.</p>