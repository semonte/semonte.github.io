---
layout: post
title: Proxy Pattern
tags: [structural]
---

<h1>Proxy Pattern</h1>

<p><a href="https://en.wikipedia.org/wiki/Proxy_pattern">Wikipedia</a> describes proxy pattern as following</p>

<p><i>A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate.</i></p>
  
<h2>Use Case</h2>

<p>I have many files in the OS and some of those files should be protected from access. How could I achieve this using Java?</p>

<h2>Solution</h2>

<p>We'll solve this problem by using the proxy pattern and Java reflection API. The idea is to create an interface, which reads a file's content. If the file is protected, it throws an <i>AccessDeniedException</i> exception, othewise the content is returned.</p>

<p>Let's start by writing the interface.</p>

<pre>
public interface IFile {
    public String getContent(String path);
}
</pre>

<p>And here we have the implementation of that interface.</p>

<pre>
public class FileImpl implements IFile {

public String getContent(String path) {
  // This method would actually read a file's content
  return "Placeholder text...";
  }
}
</pre>

<p>In order to use a proxy instead of using <i>FileImpl</i>, we need a way to create that proxy. Luckily, Java has a pretty awesome class in <i>java.lang.reflect</i> package called <i>Proxy</i>. By using <i>Proxy</i> class, we can actually create proxies from interfaces!</p>

<pre>
IFile o = (IFile) Proxy.newProxyInstance(IFile.class.getClassLoader(),
                                         new Class<?>[]{IFile.class},
                                         new FileInvocationHandler(new FileImpl()));
</pre>

<p>You are probably now wondering what is that <i>FileInvocationHandler</i>. Here is the source for that and an explantion after.</p>

<pre>

public class FileInvocationHandler implements InvocationHandler {

    private IFile file;

    public FileInvocationHandler(IFile file) {
        this.file = file;
    }

    public Object invoke(Object o, Method method, Object[] args) throws Throwable {
        if (method.getName().equals("getContent")) {
            String path = (String) args[0];
            if (path.startsWith("/private")) {
                throw new AccessControlException(String.format("You are not allowed to access %s", path));
            }
        }
        return method.invoke(this.file, args);
    }
}
</pre>

<p>When we create a proxy class with the <i>Proxy.newProxyInstance()</i> method, we must also pass an invocation handler as the third parameter. When a proxy class method is called, the call actually invokes the invocation handler's invoke method, no matter what proxy method is being called. This allows us to protect files, so that the contents of files in the "/private" folder are never accessable.</p>

<p>Here are simple test cases to verify the functionality.</p>

<pre>

    @Test
    public void thatPublicFolderIsAccessible() throws IllegalAccessException {
        IFile o = (IFile) Proxy.newProxyInstance(IFile.class.getClassLoader(),
                                                 new Class<?>[]{IFile.class},
                                                 new FileInvocationHandler(new FileImpl()));
        System.out.println(o.getContent("/public/file.txt"));
        // Prints "Placeholder text..."
    }

    @Test(expected = java.security.AccessControlException.class)
    public void thatPrivateFolderIsNotAccessible() {
        IFile o = (IFile) Proxy.newProxyInstance(IFile.class.getClassLoader(),
                                                 new Class<?>[]{IFile.class},
                                                 new FileInvocationHandler(new FileImpl()));
        o.getContent("/private/file.txt");
    }
</pre>

<p class="warning">This code is here just to give you an idea of proxy pattern. There should probably be additional checks to fully secure file paths.</p>