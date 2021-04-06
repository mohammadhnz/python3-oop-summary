# Object-Oriented Design

## Introducing object-oriented

Software objects may not be tangible things that you can pick up, sense, or feel, but they are models of something that can do certain things and have certain things done to them. Formally, an object is a collection of data and associated behaviors.

object-oriented means functionally directed toward modeling objects. This is one of many techniques used for modeling complex systems. It is defined by describing a collection of interacting objects via their data and behavior.

Object-oriented analysis (OOA) is the process of looking at a problem, system, or task (that somebody wants to turn into an application) and identifying the objects and interactions between those objects. The analysis stage is all about what needs to be done.

Object-oriented design (OOD) is the process of converting such requirements into an implementation specification. The designer must name the objects, define the behaviors, and formally specify which objects can activate specific behaviors on other objects. The design stage is all about how things should be done.

Object-oriented programming (OOP) is the process of converting this perfectly-defined design into a working program that does exactly what the CEO originally requested.

## Objects and classes
In object-oriented modeling, the term used for a kind of object is class.

UML is very popular among managers, and occasionally disparaged by programmers. The syntax of a UML diagram is generally pretty obvious; you don't have to read a tutorial to (mostly) understand what is going on when you see one. UML is also fairly easy to draw, and quite intuitive. After all, many people, when describing classes and their relationships, will naturally draw boxes with lines between them. Having a standard based on these intuitive diagrams makes it easy for programmers to communicate with designers, managers, and each other.

The association between classes is often obvious and needs no further explanation, but we have the option to add further clarification as needed.

## Data describes objects

Data represents the individual characteristics of a certain object. A class can define specific sets of characteristics that are shared by all objects from that class. Attributes are frequently referred to as members or properties.

Usually, we don't need to be overly concerned with data types at the design stage, as implementation-specific details are chosen during the programming stage. Generic names are normally sufficient for design.

## Behaviors are actions

Now that we know what data is, the last undefined term is behaviors. Behaviors are actions that can occur on an object. The behaviors that can be performed on a specific class of object are called methods. At the programming level, methods are like functions in structured programming, but they magically have access to all the data associated with this object. Like functions, methods can also accept parameters and return values.

## Hiding details and creating the public interface
The key purpose of modeling an object in object-oriented design is to determine what the public interface of that object will be. The interface is the collection of attributes and methods that other objects can access to interact with that object. They do not need, and are often not allowed, to access the internal workings of the object.

This process of hiding the implementation of an object is suitably called information hiding. It is also sometimes referred to as encapsulation, but encapsulation is actually a more all-encompassing term. Encapsulated data is not necessarily hidden. Encapsulation is, literally, creating a capsule (think of creating a time capsule). If you put a bunch of information into a time capsule, and lock and bury it, it is both encapsulated and the information is hidden. On the other hand, if the time capsule, has not been buried and is unlocked or made of clear plastic, the items inside it are still encapsulated, but there is no information hiding.

The public interface, however, is very important. It needs to be carefully designed as it is difficult to change it in the future. Changing the interface will break any client objects that are accessing it. We can change the internals all we like, for example, to make it more efficient, or to access data over the network as well as locally, and the client objects will still be able to talk to it, unmodified, using the public interface. On the other hand, if we alter the interface by changing publicly accessed attribute names or the order or types of arguments that a method can accept, all client classes will also have to be modified. When designing public interfaces, keep it simple. Always design the interface of an object based on how easy it is to use, not how hard it is to code (this advice applies to user interfaces as well).

The model is an abstraction of a real concept.

Abstraction is another object-oriented term related to encapsulation and information hiding. Abstraction means dealing with the level of detail that is most appropriate to a given task. It is the process of extracting a public interface from the inner details.

abstraction is the process of encapsulating information with separate public and private interfaces. The private interfaces can be subject to information hiding.

## Composition

most design patterns rely on two basic object- oriented principles known as composition and inheritance. Composition is simpler, so let's start with it.

Composition is the act of collecting several objects together to create a new one. Aggregation is almost exactly like composition. The difference is that aggregate objects can exist independently.

Another way to differentiate between aggregation and composition is to think about the lifespan of the object. If the composite (outside) object controls when the related (inside) objects are created and destroyed, composition is most suitable. If the related object is created independently of the composite object, or can outlast that object, an aggregate relationship makes more sense. Also, keep in mind that composition is aggregation; aggregation is simply a more general form of composition. Any composite relationship is also an aggregate relationship, but not vice versa.

The composition relationship is represented in UML as a solid diamond. The hollow diamond represents the aggregate relationship.

## Inheritance

The is a relationship is formed by inheritance. Inheritance is the most famous, well-known, and over-used relationship in object-oriented programming.

Abstract methods basically say this: We demand this method exist in any non-abstract subclass, but we are declining to specify an implementation in this class.

## Inheritance provides abstraction

Polymorphism is the ability to treat a class differently, depending on which subclass is implemented.

Polymorphism is pretty cool, but it is a word that is rarely used in Python programming.
Python goes an extra step past allowing a subclass of an object to be treated like a parent
class. A board implemented in Python could take any object that has a move method,
whether it is a bishop piece, a car, or a duck. When move is called, the Bishop will move
diagonally on the board, the car will drive someplace, and the duck will swim or fly,
depending on its mood.

This sort of polymorphism in Python is typically referred to as duck typing: if it walks like a duck or swims like a duck, it's a duck. We don't care if it really is a duck (is a being a cornerstone of inheritance), only that it swims or walks. Geese and swans might easily be able to provide the duck-like behavior we are looking for. This allows future designers to create new types of birds without actually specifying an inheritance hierarchy for aquatic birds. It also allows them to create completely different drop-in behaviors that the original designers never planned for. For example, future designers might be able to make a walking, swimming penguin that works with the same interface without ever suggesting that penguins are ducks.

## Multiple inheritance

Object-oriented design can also feature such multiple inheritance, which allows a subclass to inherit functionality from multiple parent classes.

As long as two classes have distinct interfaces, it is not normally harmful for a subclass to inherit from both of them. However, it gets messy if we inherit from two classes that provide overlapping interfaces.

Inheritance is the perfect solution for obvious is a relationships, but it can be abused. Programmers often use inheritance to share code between two kinds of objects that are only distantly related, with no is a relationship in sight.

### End
