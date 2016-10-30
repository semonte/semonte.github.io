---
layout: post
title: Decorator Pattern
tags: [structural]
---

<h1>Decorator Pattern</h1>

<p><a href="https://en.wikipedia.org/wiki/Decorator_pattern">Wikipedia</a> describes the decorator pattern as following</p>

<p><i>In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class.</i></p>

<h2>Use Case</h2>

<p>We need to create cocktails for our newly opened bar. There are hundreds of cocktails available so we need a way to build these cocktails easily, so that our supply can be easily extended in the future. Luckily, all cocktails are made up of rather the same ingredients, we'll just have to use these ingredients differently in each cocktail.</p>

<h2>Solution</h2>

<p>Decorator pattern is a real lifesaver here. It allows us to decorate an object to provide additional behavior for that object. What are we going to decorate here? A cocktail of course!</p>

<pre>

public abstract class Cocktail {
    public abstract BigDecimal getCost();
    public abstract String getDescription();
}
</pre>

<p>Our cocktails have only two behaviors, they know their value and what they contain. Now it is time for our base implementation of the cocktail. Our basic cocktail will always contain some Vodka. This will be the base class that we'll start decorating.</p>

<pre>

public class BasicCocktail extends Cocktail {

    public BigDecimal getCost() {
        return new BigDecimal(3);
    }

    public String getDescription() {
        return "Vodka";
    }
}
</pre>

<p>Just like with our base cocktail class, we'll also use a base decorator class. Note that this base decorator class also extends the <i>Cocktail</i> class just like the <i>BasicCocktail</i> class.</p>

<pre>

public class CocktailDecorator extends Cocktail {

    protected final Cocktail cocktail;

    public CocktailDecorator(Cocktail cocktail) {
        this.cocktail = cocktail;
    }

    public BigDecimal getCost() {
        return cocktail.getCost();
    }

    public String getDescription() {
        return cocktail.getDescription();
    }
}
</pre>

<p>Here is a sample decorator that will add White Rum to the cocktail.</p>

<pre>

public class WhiteRum extends CocktailDecorator {

    public WhiteRum(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(3));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", White rum";
    }
}
</pre>

<p>All the rest of the decorators are the same, with their own cost and description.</p>

<pre>

public class TripleSec extends CocktailDecorator {

    public TripleSec(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(3));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Triple sec";
    }
}

public class Tequila extends CocktailDecorator {

    public Tequila(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(3));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Tequila";
    }
}

public class LemonJuice extends CocktailDecorator {

    public LemonJuice(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(1));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Lemon juice";
    }
}

public class Gin extends CocktailDecorator {

    public Gin(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(3));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Gin";
    }
}

public class Cola extends CocktailDecorator {

    public Cola(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(2));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Cola";
    }
}
</pre>

<p>Here is how we would build a cocktail using the decorators. Let's start by creating the basic cocktail.</p>

<pre>

        Cocktail cocktail = new BasicCocktail();
        System.out.println(cocktail.getDescription());
        // Prints: Vodka
</pre>

<p>To add white rum to the mix.</p>

<pre>

        cocktail = new WhiteRum(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum
</pre>

<p>Let's add the rest as well!</p>

<pre>

        cocktail = new TripleSec(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum, Triple sec
        
        cocktail = new Tequila(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum, Triple sec, Tequila
        
        cocktail = new LemonJuice(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints:Vodka, White rum, Triple sec, Tequila, Lemon juice

        cocktail = new Gin(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum, Triple sec, Tequila, Lemon juice, Gin

        cocktail = new Cola(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum, Triple sec, Tequila, Lemon juice, Gin, Cola
        
        System.out.println("Cost: " + cocktail.getCost());
        // Prints: Cost: 18
        
</pre>

<p>But wait! Now our customers are complaining that the drink is <b>too warm and needs to be colder</b>. Luckily, we are well prepared for such demands. Here is a new decorator to make our drink a bit cooler.</p>

<pre>

public class Ice extends CocktailDecorator {

    public Ice(Cocktail cocktail) {
        super(cocktail);
    }

    public BigDecimal getCost() {
        return cocktail.getCost().add(new BigDecimal(0.5));
    }

    public String getDescription() {
        return cocktail.getDescription() + ", Ice";
    }
}
</pre>

<p>We can add this decorator the same way as all the others</p>

<pre>

        cocktail = new Ice(cocktail);
        System.out.println(cocktail.getDescription());
        // Prints: Vodka, White rum, Triple sec, Tequila, Lemon juice, Gin, Cola, Ice
</pre>

<p>Here is our finished drink!</p>

<div><img src="/images/posts/cocktail.jpg" title="Cocktail with Decorator Pattern"></div>
<div><i>Image from <a href="http://en.wikipedia.org/wiki/File:Long_Island_Ice_Tea.jpg#file">Wikipedia</a></i><div>