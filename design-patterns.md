<h1>Design Patterns</h1>

---
## <u>Bridge Pattern</u>
---

- Separates abstraction from implementation. 
- Allows independent changes. 
- Bridges high-level logic and low-level logic.
- Client interacts with abstraction instead of the implementation. 

### Components:

- <u>Abstraction:</u> Defines a high-level interface that the client uses. This contains a reference to the implementation and delegates the actual implementation to the Implementor interface. This abstraction can be extended and more details added to it if needed.
- <u>Implementors:</u> Declares the interface for the implementation classes. Does not have to correspond directly to the abstraction interface and can be different.
- <u>Concrete Implementor:</u> Implements teh Implementor interface and provides implementations for the low-level operations. 

### Java Example:

- Consider a drawing application where we can draw shapes in different platforms. 

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

- <u>Concrete implementors:</u> Each implementation gives specific use for the interface and the `drawCircle()` method. This could be used to specific implementations for different platforms. 

- <u>Abstraction:</u> This represents a *high-level* shape. It references the `DrawingAPI` object which is the *low-level* implementation and the constructor initializes that object. A `Shape` also has an abstract method `draw()` that concrete shapes will have to implement.

- <u>Refined abstraction:</u> This class adds specific attributes to the *abstract* `Shape` class. In the example, a `CircleShape` is created and now our service is able to draw circles using those attributes. If another shape, such as a square was to be added to our service, a class such as `SquareShape` could be created with specific attributes for a square as long as the *Implementors* were also updated. 

- <u>Client:</u> The Client instantiates the service `api1` and `api2`, it creates two circle objects `circle1` and `circle2` with different values and finally uses the `draw()` method to draw the shapes. 

### Conclusion

The bridge pattern allows a same service to be used by different platforms, without conflicting between the different uses. The service can be implemented and changed independently. Using a bridge promotes the Open-Closed Principle (OCP), as both the
Shape and Colour hierarchies can be extended independently of one another

---
## <u>Decorator pattern</u>
---

- Allows behaviour to be added to individual objects statically or dinamically, without affecting the behaviour of other objects from the same class.

### Components:

- <u>Component:</u> Defines the interface for the objects that can have responsabilities added to them dynamically.
- <u>Concrete Component:</u> Implements the Component interface and defines a concrete object to which additional responsibilities can be added.
- <u>Decorator:</u> Maintains a reference to a Component object and conforms to the Component interface. It also has an association with a Concrete Component. 
- <u>Concrete Decorator:</u> Adds new responsibilities to the component. It extends the functionality of the Component by adding new state or behavior. 

### Java Example:

- Consider a coffe shop, where we have a `coffee` component and decorators like `milk`, `sugar` and `whipCream` that can customize the coffe. 

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

- <u>Concrete Component:</u> This implements the `Coffee` interface. This represent what the simplest coffee option is and it's cost.  

- <u>Decorator:</u> This points to the concrete component `Coffee` and provide default implementations for the coffee methods delegating it to the `decoratedCoffee`. This means that any change to a coffee will use these methods. 

- <u>Concrete Decorators:</u> These extend the abstract *Decorator* and implements the default methods. These define exactly how each "decoration" to a coffee is done by adding to the default cost and description. 

- <u>Client:</u> The Client instantiates a `SimpleCoffee()` and then adds each decoration to the coffee object. The object updates its cost and description according to each *Decorator* call.

### Conclusion

The decorator pattern allows you to create a flexible and extensible system where you can add new functionalities(Decorators) to objects(Components) without changing their structure. In the example above, the cost and description are dynamically adjusted based on the added decorators.

---
## <u>New pattern</u>
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

---
## <u>New pattern</u>
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




