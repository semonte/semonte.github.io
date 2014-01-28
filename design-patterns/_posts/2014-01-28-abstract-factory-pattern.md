---
layout: post
title: Abstract Factory Pattern
---

<h1>Abstract Factory Pattern</h1>

<p>Abstract factory is a tough one! At least when you try to understand the definition of it and a suitable use case. Let's jump straight to the code to see what is going on!</p>
  
<h2>Use Case</h2>

<p>I have a user service that needs to get users either from database or cache. The service method is the following.</p>

{% highlight java %}
public User getUserByName(RepositoryType repositoryType, String name)
{% endhighlight %}

{% highlight java %}
public class User {

    public String name;

    public User(String name) {
        this.name = name;
    }
}
{% endhighlight %}

<p>The RepositoryType is an enum, which specifies what repository should be used (database or cache).</p>
  
{% highlight java %}
public enum RepositoryType {
    DATABASE, CACHE
}
{% endhighlight %}

<p>How can I isolate the logic of selecting the correct repository, so that the user service does not need to bother itself with such low level details. What can we do?</p>

<h2>Solution</h2>

<p>We'll start by creating an interface for finding the user by name. We do not worry about selecting the correct repository yet.</p>

{% highlight java %}
public interface IUserRepository {
    public User getUserByName(String name);
}
{% endhighlight %}

<p>Next we provide two implementations for this interface (database and cached implementations).</p>

{% highlight java %}
public class DatabaseUserRepository implements IUserRepository {

    public User getUserByName(String name) {
        return new User("I am a user from database!");
    }
}
{% endhighlight %}

{% highlight java %}
public class CacheUserRepository implements IUserRepository {

    public User getUserByName(String name) {
        return new User("I am a user from cache!");
    }
}
{% endhighlight %}

<p>We are almost done. Now we just need to select the correct repository based on the RepositoryType parameter of the user service call. Easy, let's just do the following.</p>

{% highlight java %}
public class UserService {

    private IUserRepositoryFactory userRepositoryFactory;

    public UserService(IUserRepositoryFactory userRepositoryFactory) {
        this.userRepositoryFactory = userRepositoryFactory;
    }

    public User getUserByName(RepositoryType repositoryType, String name) {
        return this.userRepositoryFactory.create(repositoryType).getUserByName(name);
    }
}
{% endhighlight %}

<p>Where did that IUserRepositoryFactory come from and what does it do? Hmm...</p>

{% highlight java %}
public interface IUserRepositoryFactory {
    public IUserRepository create(RepositoryType repositoryType);
}
{% endhighlight %}

{% highlight java %}
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
{% endhighlight %}

<p>As you can see, IUserRepositoryFactory is an interface which is implemented by UserRepositoryFactory. The UserRepositoryFactory is responsible for the logic of selecting the correct repository. The user service is completely unaware of this logic and we could easily add more repositories just be modifying the UserRepositoryFactory class!</p>

<p>Here is an example of how the service is used. <b>Note: the first four lines would actually be handled by a dependency injection framework.</b></p>
{% highlight java %}
IUserRepository databaseUserRepository = new DatabaseUserRepository();
IUserRepository cacheUserRepository = new CacheUserRepository();
IUserRepositoryFactory userRepositoryFactory = new UserRepositoryFactory(databaseUserRepository, cacheUserRepository);
UserService userService = new UserService(userRepositoryFactory);

User userFromDatabase = userService.getUserByName(RepositoryType.DATABASE, "");
User userFromCache = userService.getUserByName(RepositoryType.CACHE, "");

assertEquals("I am a user from database!", userFromDatabase.name);
assertEquals("I am a user from cache!", userFromCache.name);
{% endhighlight %}

<p>I hope that <a href="http://en.wikipedia.org/wiki/Abstract_factory_pattern">Wikipedia's</a> first line about abstract factories makes even a bit more sense to you than before :)</p>