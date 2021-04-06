# Objects in Python

## Initializing the object

Most object-oriented programming languages have the concept of a constructor, a special method that creates and initializes the object when it is created. Python is a little different; it has a constructor and an initializer. The constructor function is rarely used, unless you're doing something very exotic. So, we'll start our discussion with the much more common initialization method.

The Python initialization method is the same as any other method, except it has a special name, \_\_init\_\_. The leading and trailing double underscores mean this is a special method that the Python interpreter will treat as a special case.

Most of the time, we put our initialization statements in an \_\_init\_\_ function. But as mentioned earlier, Python has a constructor in addition to its initialization function. You may never need to use the other Python constructor

The constructor function is called \_\_new\_\_ as opposed to \_\_init\_\_, and accepts exactly one argument; the class that is being constructed (it is called before the object is constructed, so there is no self argument). It also has to return the newly created object. This has interesting possibilities when it comes to the complicated art of metaprogramming, but is not very useful in day-to-day Python. In practice, you will rarely, if ever, need to use \_\_new\_\_. The\_\_init\_\_ method will almost always be sufficient.

## Explaining yourself

Python is an extremely easy-to-read programming language; some might say it is self- documenting. However, when carrying out object-oriented programming, it is important to write API documentation that clearly summarizes what each object and method does. Keeping documentation up to date is difficult; the best way to do it is to write it right into our code.

Python supports this through the use of docstrings. Each class, function, or method header can have a standard Python string as the first line following the definition (the line that ends in a colon). This line should be indented the same as the code that follows it. 
Docstrings are simply Python strings enclosed with apostrophes (') or quotation marks (") characters. Often, docstrings are quite long and span multiple lines (the style guide suggests that the line length should not exceed 80 characters), which can be formatted as multi-line strings, enclosed in matching triple apostrophe (''') or triple quote (""") characters. 
A docstring should clearly and concisely summarize the purpose of the class or method it is describing. It should explain any parameters whose usage is not immediately obvious, and is also a good place to include short examples of how to use the API. Any caveats or problems an unsuspecting user of the API should be aware of should also be noted.
```
class Point:
	"Represents a point in two-dimensional geometric coordinates" 
	def __init__(self, x=0, y=0):
	  """Initialize the position of a new point. The x and y coordinates can be specified. If they are 		not, the point defaults to the origin.""" 
	  self.move(x, y) 
	def move(self, x, y):
	  "Move the point to a new location in 2D space."
	  self.x = x
	  self.y = y
```

## Modules and packages

Some sources say that we can import all classes and functions from the database module using this syntax: 
```
from database import *
```
Don't do this. Most experienced Python programmers will tell you that you should never use this syntax (a few will tell you there are some very specific situations where it is useful, but I disagree). They'll use obscure justifications such as it clutters up the namespace, which doesn't make much sense to beginners.

When we explicitly import the database class at the top of our file using from database import Database, we can easily see where the Database class comes from. We might use db = Database() 400 lines later in the file, and we can quickly look at the imports to see where that Database class came from. Then, if we need clarification as to how to use the Database class, we can visit the original file (or import the module in the interactive interpreter and use the help(database.Database) command). However, if we use the from database import * syntax, it takes a lot longer to find where that class is located. Code maintenance becomes a nightmare. 

In addition, most code editors are able to provide extra functionality, such as reliable code completion, the ability to jump to the definition of a class, or inline documentation, if normal imports are used. The import * syntax usually completely destroys their ability to do this reliably. Finally, using the import * syntax can bring unexpected objects into our local namespace. Sure, it will import all the classes and functions defined in the module being imported from, but it will also import any classes or modules that were themselves imported into that file!

## Organizing modules

A package is a collection of modules in a folder. The name of the package is the name of the folder. We need to tell Python that a folder is a package to distinguish it from other folders in the directory. To do this, place a (normally empty) file in the folder named __init__.py. If we forget this file, we won't be able to import modules from that folder.


## Relative imports

we can import code directly from packages, as opposed to just modules inside packages. In this example, we have an ecommerce package containing two modules named database.py and products.py. The database module contains a db variable that is accessed from a lot of places.

Remember the \_\_init\_\_.py file that defines a directory as a package? This file can contain any variable or class declarations we like, and they will be available as part of the package. In our example, if the ecommerce \_\_init\_\_.py file contained the following line: from .database import db We could then access the db attribute from main.py or any other file using the following import: from ecommerce import db

It might help to think of the \_\_init\_\_.py file as if it were an ecommerce.py file, if that file were a module instead of a package. This can also be useful if you put all your code in a single module and later decide to break it up into a package of modules. The \_\_init\_\_.py file for the new package can still be the main point of contact for other modules talking to it, but the code can be internally organized into several different modules or subpackages. I recommend not putting much code in an \_\_init\_\_.py file, though. Programmers do not expect actual logic to happen in this file, and much like with from x import *, it can trip them up if they are looking for the declaration of a particular piece of code and can't find it until they check \_\_init\_\_.py.

## Organizing module content

ll module-level code is executed immediately at the time it is imported. However, if it is inside a method or function, the function will be created, but its internal code will not be executed until the function is called. This can be a tricky thing for scripts that perform execution (such as the main script in our e-commerce example). Sometimes, we write a program that does something useful, and then later find that we want to import a function or class from that module into a different program. However, as soon as we import it, any code at the module level is immediately executed. If we are not careful, we can end up running the first program when we really only meant to access a couple of functions inside that module. 
To solve this, we should always put our start up code in a function (conventionally, called main) and only execute that function when we know we are running the module as a script, but not when our code is being imported from a different script. We can do this by guarding the call to main inside a conditional statement, demonstrated as follows:

class UsefulClass: """This class might be useful to other modules.""" pass def main(): """Creates a useful class and does something with it for our module."""
```
useful = UsefulClass() 
print(useful)
if __name__ == "__main__":
  main()
```
Every module has a \_\_name\_\_ special variable (remember, Python uses double underscores for special variables, such as a class's \_\_init\_\_ method) that specifies the name of the module when it was imported. When the module is executed directly with python module.py, it is never imported, so the \_\_name\_\_ is arbitrarily set to the "\_\_main\_\_" string. Make it a policy to wrap all your scripts in an if \_\_name\_\_ == "\_\_main\_\_": test, just in case you write a function that you may want to be imported by other code at some point in the future.

## Who can access my data? 
Most object-oriented programming languages have a concept of access control. This is related to abstraction. Some attributes and methods on an object are marked private, meaning only that object can access them.Others are marked protected, meaning only that class and any subclasses have access. The rest are public, meaning any other object is allowed to access them.
Python doesn't do this. Python doesn't really believe in enforcing laws that might someday get in your way. Instead, it provides unenforced guidelines and best practices. Technically, all methods and attributes on a class are publicly available. If we want to suggest that a method should not be used publicly, we can put a note in docstrings indicating that the method is meant for internal use only (preferably, with an explanation of how the public- facing API works!). 
By convention, we should also prefix an internal attribute or method with an underscore character, _. Python programmers will interpret this as this is an internal variable, think three times before accessing it directly. But there is nothing inside the interpreter to stop them from accessing it if they think it is in their best interest to do so. Because, if they think so, why should we stop them? We may not have any idea what future uses our classes may be put to.

There's another thing you can do to strongly suggest that outside objects don't access a property or method: prefix it with a double underscore, \_\_. This will perform name mangling on the attribute in question. In essence, name mangling means that the method can still be called by outside objects if they really want to do so, but it requires extra work and is a strong indicator that you demand that your attribute remains private. Here is an example code snippet:
```
class SecretString:
  """A not-at-all secure way to store a secret string."""
  def __init__(self, plain_string, pass_phrase):
    self.__plain_string = plain_string
    self.__pass_phrase = pass_phrase
  def decrypt(self, pass_phrase):
    """Only show the string if the pass_phrase is correct."""
    if pass_phrase == self.__pass_phrase:
      return self.__plain_stri
```
let's see how easy it can be to hack our security:
```
print(secret_string._SecretString__plain_string)
ACME: Top Secret
```

This is Python name mangling at work. When we use a double underscore, the property is prefixed with _&lt;classname&gt;. When methods in the class internally access the variable, they are automatically unmangled. When external classes wish to access it, they have to do the name mangling themselves. So, name mangling does not guarantee privacy; it only strongly recommends it. Most Python programmers will not touch a double underscore variable on another object unless they have an extremely compelling reason to do so.

## End
