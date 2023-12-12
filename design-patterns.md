# Design Patterns

## Table of Contents

- [Bridge Pattern](#bridge-pattern)
- [Decorator Pattern](#decorator-pattern)
- [Facade Pattern](#facade-pattern)
- [Adapter Pattern](#adapter-pattern)
- [Strategy Pattern](#strategy-pattern)
- [State Pattern](#state-pattern)

---
## Bridge Pattern
---

- Separates abstraction from implementation. 
- Allows independent changes. 
- Bridges high-level logic and low-level logic. The client interacts with abstraction instead of the implementation. 

### Components:

- <u>Abstraction:</u> Defines a high-level interface that the client uses. This contains a reference to the implementation and delegates the actual implementation to the Implementor interface. This abstraction can be extended and more details added to it if needed.
- <u>Implementors:</u> Declares the interface for the implementation classes. Does not have to correspond directly to the abstraction interface and can be different.
- <u>Concrete Implementor:</u> Implements the Implementor interface and provides implementations for the low-level operations. 

### Java Example:

- Consider a drawing application where we can draw shapes on different platforms. 

```java
// Implementor Interface
public interface DrawingAPI {
    void drawCircle(int x, int y, int radius);
}


// Concrete implementors
// Concrete Implementor 1
public class DrawingAPI1 implements DrawingAPI {
    @Override
    public void drawCircle(int x, int y, int radius) {
        System.out.println("Drawing Circle at (" + x + ", " + y + ") with radius " + radius + " using API1");
    }
}

// Concrete Implementor 2
public class DrawingAPI2 implements DrawingAPI {
    @Override
    public void drawCircle(int x, int y, int radius) {
        System.out.println("Drawing Circle at (" + x + ", " + y + ") with radius " + radius + " using API2");
    }
}

// Abstraction
public abstract class Shape {
    protected DrawingAPI drawingAPI;

    protected Shape(DrawingAPI drawingAPI) {
        this.drawingAPI = drawingAPI;
    }

    public abstract void draw(); // Abstraction-specific method
}

// Refined Abstraction
public class CircleShape extends Shape {

    private int x, y, radius;

    public CircleShape(int x, int y, int radius, DrawingAPI drawingAPI) {
        super(drawingAPI);
        this.x = x;
        this.y = y;
        this.radius = radius;
    }

    @Override
    public void draw() {
        drawingAPI.drawCircle(x, y, radius);
    }
}

// Client
public class Client {
    public static void main(String[] args) {
        DrawingAPI api1 = new DrawingAPI1();
        DrawingAPI api2 = new DrawingAPI2();

        Shape circle1 = new CircleShape(1, 2, 3, api1);
        Shape circle2 = new CircleShape(5, 7, 11, api2);

        circle1.draw();
        circle2.draw();
    }
}

``` 
- <u>Implementor interface:</u> This is where the drawing service interface is defined and also the *bridge* between the abstraction(`Shape`) and its implementations `DrawingAPI1` and `DrawingAPI2`. 

- <u>Concrete implementors:</u> Each implementation gives specific use for the interface and the `drawCircle()` method. This could be used for specific implementations for different platforms. 

- <u>Abstraction:</u> This represents a *high-level* shape. It references the `DrawingAPI` object which is the *low-level* implementation and the constructor initializes that object. A `Shape` also has an abstract method `draw()` that concrete shapes will have to implement.

- <u>Refined abstraction:</u> This class adds specific attributes to the *abstract* `Shape` class. In the example, a `CircleShape` is created and now our service can draw circles using those attributes. If another shape, such as a square was to be added to our service, a class such as `SquareShape` could be created with specific attributes for a square as long as the *Implementors* were also updated. 

- <u>Client:</u> The Client instantiates the service `api1` and `api2`, it creates two circle objects `circle1` and `circle2` with different values and finally uses the `draw()` method to draw the shapes. 

### Conclusion

The bridge pattern allows the same service to be used by different platforms, without conflict between the different uses. The service can be implemented and changed independently. Using a bridge promotes the Open-Closed Principle (OCP), as both the
Shape and Colour hierarchies can be extended independently of one another

---
## Decorator pattern
---

- Allows behavior to be added to individual objects statically or dynamically, without affecting the behavior of other objects from the same class.

### Components:

- <u>Component:</u> Defines the interface for the objects that can have responsibilities added to them dynamically.
- <u>Concrete Component:</u> Implements the Component interface and defines a concrete object to which additional responsibilities can be added.
- <u>Decorator:</u> Maintains a reference to a Component object and conforms to the Component interface. It also has an association with a Concrete Component. 
- <u>Concrete Decorator:</u> Adds new responsibilities to the component. It extends the functionality of the Component by adding a new state or behavior. 

### Java Example:

- Consider a coffee shop, where we have a `coffee` component and decorators like `milk`, `sugar` and `whipCream` that can customize the coffee. 

```java
// Component Interface
public interface Coffee {
    double cost();
    String getDescription();
}

// Concrete Component(Simple coffee)
public class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 2.0; // Base cost of simple coffee
    }

    @Override
    public String getDescription() {
        return "Simple Coffee";
    }
}

// Decorator
public abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee decoratedCoffee) {
        this.decoratedCoffee = decoratedCoffee;
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost();
    }

    @Override
    public String getDescription() {
        return decoratedCoffee.getDescription();
    }
}

// Concrete Decorator - Milk
public class Milk extends CoffeeDecorator {
    public Milk(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.5; // Additional cost of milk
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Milk";
    }
}

// Concrete Decorator - Sugar
public class Sugar extends CoffeeDecorator {
    public Sugar(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.2; // Additional cost of sugar
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Sugar";
    }
}

// Concrete Decorator - Whip Cream
public class WhipCream extends CoffeeDecorator {
    public WhipCream(Coffee decoratedCoffee) {
        super(decoratedCoffee);
    }

    @Override
    public double cost() {
        return super.cost() + 0.7; // Additional cost of whip cream
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Whip Cream";
    }
}

// Client code
public class Client {
    public static void main(String[] args) {
        // Creating a simple coffee
        Coffee coffee = new SimpleCoffee();
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());

        // Adding decorators
        coffee = new Milk(coffee);
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());

        coffee = new Sugar(coffee);
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());

        coffee = new WhipCream(coffee);
        System.out.println("Cost: $" + coffee.cost() + ", Description: " + coffee.getDescription());
    }
}

``` 
- <u>Component Interface:</u> This defines what any `coffee` is. Any coffee has a `cost()` and a `getDescription()`. 

- <u>Concrete Component:</u> This implements the `Coffee` interface. This represents what the simplest coffee option is and it's cost.  

- <u>Decorator:</u> This points to the concrete component `Coffee` and provides default implementations for the coffee methods delegating it to the `decoratedCoffee`. This means that any change to a coffee will use these methods. 

- <u>Concrete Decorators:</u> These extend the abstract _Decorator_ and implement the default methods. These define exactly how each "decoration" to a coffee is done by adding to the default cost and description. 

- <u>Client:</u> The Client instantiates a `SimpleCoffee()` and then adds each decoration to the coffee object. The object updates its cost and description according to each *Decorator* call.

### Conclusion

The decorator pattern allows you to create a flexible and extensible system where you can add new functionalities(Decorators) to objects(Components) without changing their structure. In the example above, the cost and description are dynamically adjusted based on the added decorators.

---
## Facade pattern
---

- A *Facade* pattern provides a simplified interface to a complex system. It acts as a unified high-level interface to a set of interfaces and/or classes within a subsystem **hiding the complexity** of the subsystem from clients.

### Components:

- <u>Facade:</u> Defines a simplified interface to the subsystem. Manages interactions between client and subsystem. Does not perform actual work, instead, it delegates it to the subsystem components.  
- <u>Subsystems:</u> These are classes/interfaces that perform specific tasks. The *Facade Pattern* does not eliminate the complexity of the subsystem; It provides a more convenient way to access it.  

### Java Example:

- Let's use a home theater system as an example of this design pattern. The classes `DVDPlayer`, `Amplifier` and `Projector` are individual components of the subsystem and they perform specific actions.

```java
// Facade
public class HomeTheaterFacade {
    private DVDPlayer dvdPlayer; // Individual component
    private Amplifier amplifier; // Individual component
    private Projector projector; // Individual component

    public HomeTheaterFacade(DVDPlayer dvdPlayer, Amplifier amplifier, Projector projector) {
        this.dvdPlayer = dvdPlayer;
        this.amplifier = amplifier;
        this.projector = projector;
    }

    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        projector.on();
        amplifier.on();
        dvdPlayer.on();
        dvdPlayer.play(movie);
    }

    public void endMovie() {
        System.out.println("Shutting down the home theater...");
        dvdPlayer.off();
        amplifier.off();
        projector.off();
    }
}

// Client
public class Client {
    public static void main(String[] args) {
        DVDPlayer dvdPlayer = new DVDPlayer();
        Amplifier amplifier = new Amplifier();
        Projector projector = new Projector();

        HomeTheaterFacade facade = new HomeTheaterFacade(dvdPlayer, amplifier, projector);

        facade.watchMovie("Inception"); // Client accesses facade to perform the action of watching a movie.
        System.out.println("Enjoying the movie...");

        facade.endMovie();
    }
}

``` 
- <u>Components:</u> These have specific actions they can perform. The client can use these functions but never by directly accessing them. 
- <u>Facade:</u> The facade gathers all the functionality from the individual components and gives the user a simpler way to use them. The client calls the function `watchMovie()` but is the interface that tells the components to turn on, etc, for the client to watch the movie.
- <u>Client:</u> The client knows how to watch a movie and how to end a movie. The low-level actions needed for these to happen are dealt with by the components, the user does not know and does not care about how it is done.

### Conclusion

- This pattern can be used to **decouple** the client code from the inner workings of the system and also to **reduce dependencies** between the *client* and individual *components* of the subsystem.

---
## Adapter pattern
---

- This is a pattern that **allows objects with incompatible interfaces to work together**. This is done without modifying their source code. This pattern is useful for an interface that doesn't work with the client code.

### Components:

- <u>Target:</u> Defines the interface that the client code expects.
- <u>Adapter:</u> This implements the *Target* interface while containing an instance of the *Adaptee*. This adapts the *Adaptee's* interface to match the *Target* interface. It acts as a translator for the *Adaptee*. 
- <u>Adaptee:</u> This is the class or interface that needs to be adapted. It has an interface that is incompatible with the *Target* interface.

### Java Example:

- Consider a legacy system that calculates areas of shapes using a `LegacyShapeCalculator`. We want to implement a new code that expects a `Shape` interface for various shape objects. 

```java
// Target interface
public interface Shape {
    double calculateArea();
}

// Adaptee (legacy code)
public class LegacyShapeCalculator {
    public double calculateAreaOfSquare(double side) {
        return side * side;
    }
}

// Adapter
public class SquareAdapter implements Shape {
    private LegacyShapeCalculator legacyCalculator;

    public SquareAdapter(LegacyShapeCalculator legacyCalculator) {
        this.legacyCalculator = legacyCalculator;
    }

    @Override
    public double calculateArea() {
        double side = 5.0; // Assume a default side length
        return legacyCalculator.calculateAreaOfSquare(side);
    }
}

public class Client {
    public static void main(String[] args) {
        LegacyShapeCalculator legacyCalculator = new LegacyShapeCalculator();
        Shape square = new SquareAdapter(legacyCalculator);

        double area = square.calculateArea();
        System.out.println("Area of square: " + area);
    }
}

``` 
- <u>Target:</u> This is the interface that the client code will interact with. 
- <u>Adaptee:</u> This is the incompatible legacy class that calculates the area of a square.
- <u>Adapter:</u> This implements the `Shape` interface for the client to use in the future. It instantiates the `LegacyShapeCalculator` to adapt it to the new code. It adapts the method used to calculate the area from the legacy code so it can be re-used as `calculateArea()`
- <u>Client:</u> The client passes the instance of the legacy calculator to the `Shape` object so that the legacy code can be used. It then interacts with the `square` object(not, the legacy calculator object).

### Conclusion
- This pattern allows for incompatible code to be used through an interface that interprets that code and allows the user to access its functionalities. The user "believes" that it is communicating with the *Adaptee* and instead, it talks to the *Adapter*.

---
## Strategy pattern
---

- This is a **behavioral design pattern** that defines a family of algorithms, encapsulates each one and makes them interchangeable. It allows a client to choose the appropriate algorithm from the family of algorithms at runtime. 

### Components:

- <u>Strategy Interface:</u> This defines the common interface for all *concrete strategies* and consists of one or more methods that represent the algorithm. 

- <u>Concrete Strategies:</u> This implements the *strategy interface* and provides specific implementations for the algorithms.

- <u>Context:</u> This references the current strategy and **allows the client to switch between different strategies**. It invokes the algorithm through the *strategy interface*.

### Java Example:

- Consider a payment system that can use different payment methods(strategies), such as card, PayPal or cash.

```java

// Strategy Interface
public interface PaymentStrategy {
    void pay(int amount);
}

// Concrete Strategies
public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " dollars using credit card.");
    }
}

public class PayPalPayment implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " dollars using PayPal.");
    }
}

public class CashPayment implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("Paid " + amount + " dollars in cash.");
    }
}

// Context
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void checkout(int amount) {
        paymentStrategy.pay(amount);
    }
}

// Client Code
public class Client {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();

        // Select a payment strategy
        PaymentStrategy creditCardPayment = new CreditCardPayment();
        PaymentStrategy paypalPayment = new PayPalPayment();
        PaymentStrategy cashPayment = new CashPayment();

        // Set the selected strategy and checkout
        cart.setPaymentStrategy(creditCardPayment);
        cart.checkout(100);

        cart.setPaymentStrategy(paypalPayment);
        cart.checkout(50);

        cart.setPaymentStrategy(cashPayment);
        cart.checkout(30);
    }
}


``` 
- <u>Strategy Interface:</u> The `PaymentStrategy` interface defines that any payment method can perform a payment `pay()`.

- <u>Concrete Strategies:</u> These are concrete implementations of each type of payment. They all implement the `PaymentStrategy` interface, as they all `pay()` but this is done differently by each one.
- <u>Context:</u> The `ShoppingCart` references the current `PaymentStrategy`. It also allows for a different strategy to be set and the `checkout()` method is the one to invoke the `pay()` method of the current set payment strategy.
- <u>Client:</u> The client instantiates the `ShoppingCart` and a payment strategy. It has to set the strategy to be used and then it can perform a checkout operation. 

### Conclusion
- This design pattern defines different **strategies** that can all perform the same action. The strategy that will be used can be switched at runtime according to the user's needs.
---
## State pattern
---

- 

### Components:

- <u>:</u> 

### Java Example:

- 

```java


``` 
- <u>:</u> 

### Conclusion



