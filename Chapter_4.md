# Expecting the Unexpected
## Handling exceptions

The bare except syntax is generally frowned upon, even if you really do want to catch all instances of an exception. Use the except Exception: syntax to explicitly catch all exception types. This tell the reader that you meant to catch exception objects and all subclasses of Exception. The bare except syntax is actually the same as using except BaseException:, which actually catches system-level exceptions that are very rare to intentionally want to catch, as we'll see in the next section. If you really do want to catch them, explicitly use except BaseException: so that anyone who reads your code knows that you didn't just forget to specify what kind of exception you wanted.

We've seen several variations on the syntax for handling exceptions, but we still don't know how to execute code regardless of whether or not an exception has occurred. We also can't specify code that should be executed only if an exception does not occur. Two more keywords, finally and else, can provide the missing pieces. Neither one takes any extra arguments. The following example randomly picks an exception to throw and raises it. Then some not-so-complicated exception handling code runs that illustrates the newly introduced syntax:
```
import random 
some_exceptions = [ValueError, TypeError, IndexError, None] 
try:
	choice = random.choice(some_exceptions) 
	print("raising {}".format(choice)) 
	if choice: 
		raise choice("An error") 
except ValueError:
	print("Caught a ValueError")
except TypeError: 
	print("Caught a TypeError") 
except Exception as e:
	print("Caught some other error: %s" % ( e.__class__.__name__)) 
else:
	print("This code called if there is no exception") 
finally: 
	print("This cleanup code is always called")
```
Note how the print statement in the finally clause is executed no matter what happens. This is extremely useful when we need to perform certain tasks after our code has finished running (even if an exception has occurred). Some common examples include the following:

- Cleaning up an open database connection 
- Closing an open file 
- Sending a closing handshake over the network

The finally clause is also very important when we execute a return statement from inside a try clause. The finally handler will still be executed before the value is returned without executing any code following the try...finally clause.

Also, __pay attention__ to the output when no exception is raised: both the else and the finally clauses are executed. The else clause may seem redundant, as the code that should be executed only when no exception is raised could just be placed after the entire try...except block. The difference is that the else block __will not be executed if an exception is caught and handled__.

Any of the except, else, and finally clauses can be omitted after a try block (although else by itself is invalid). If you include more than one, the except clauses must come first, then the else clause, with the finally clause at the end. The order of the except clauses normally goes from most specific to most generic.

## The exception hierarchy

most exceptions are subclasses of the Exception class. But this is not true of all exceptions. Exception itself actually inherits from a class called BaseException. In fact, all exceptions must extend the BaseException class or one of its subclasses.

There are two key built-in the exception classes, SystemExit and KeyboardInterrupt, that derive directly from BaseException instead of Exception. The SystemExit exception is raised whenever the program exits naturally, typically because we called the sys.exit function somewhere in our code (for example, when the user selected an exit menu item, clicked the Close button on a window, or entered a command to shut down a server). The exception is designed to allow us to clean up code before the program ultimately exits. However, we generally don't need to handle it explicitly because cleanup code can happen inside a finally clause.

If we do handle it, we would normally re-raise the exception, since catching it would stop the program from exiting. There are, of course, situations where we might want to stop the program exiting; for example, if there are unsaved changes and we want to prompt the user if they really want to exit. Usually, if we handle SystemExit at all, it's because we want to do something special with it, or are anticipating it directly. We especially don't want it to be accidentally caught in generic clauses that catch all normal exceptions. This is why it derives directly from BaseException.

The KeyboardInterrupt exception is common in command-line programs. It is thrown when the user explicitly interrupts program execution with an OS-dependent key combination (normally, Ctrl + C). This is a standard way for the user to deliberately interrupt a running program, and like SystemExit, it should almost always respond by terminating the program. Also, like SystemExit, it should handle any cleanup tasks inside the finally blocks.

Here is a class diagram that fully illustrates the hierarchy:

<img src="https://user-images.githubusercontent.com/59181719/114462631-904b9800-9bf8-11eb-96e2-c303b87cf99b.png" width="500" height="350">


When we use the except: clause without specifying any type of exception, it will catch all subclasses of BaseException; which is to say, it will catch all exceptions, including the two special ones. Since we almost always want these to get special treatment, it is unwise to use the except: statement without arguments. If you want to catch all exceptions other than SystemExit and KeyboardInterrupt, explicitly catch Exception. Most Python developers assume that except: without a type is an error and will flag it in code review. If you really do want to catch everything, just explicitly use except BaseException:.

## Defining our own exceptions

### Example 1
```
class InvalidWithdrawal(Exception): 
	pass

raise InvalidWithdrawal("You don't have $50 in your account")
```
### Example 2

```

class InvalidWithdrawal(Exception):
	def __init__(self, balance, amount):
		super().__init__(f"account doesn't have ${amount}")
		self.amount = amount self.balance = balance:

	def overage(self): 
		return self.amount - self.balance
raise InvalidWithdrawal(25, 50)
```
There are many reasons for defining our own exceptions. It is often useful to add information to the exception or log it in some way. But the utility of custom exceptions truly comes to light when creating a framework, library, or API that is intended for access by other programmers. In that case, be careful to ensure your code is raising exceptions that make sense to the client programmer. They should be easy to handle and clearly describe what went on. The client programmer should easily see how to fix the error (if it reflects a bug in their code) or handle the exception (if it's a situation they need to be made aware of).

Python programmers tend to follow a model of ask forgiveness rather than permission, which is to say, they execute code and then deal with anything that goes wrong. The alternative, to look before you leap, is generally less popular. There are a few reasons for this, but the main one is that it shouldn't be necessary to burn CPU cycles looking for an unusual situation that is not going to arise in the normal path through the code. Therefore, it is wise to use exceptions for exceptional circumstances, even if those circumstances are only a little bit exceptional. Taking this argument further, we can actually see that the exception syntax is also effective for flow control. Like an if statement, exceptions can be used for decision making, branching, and message passing.

Imagine an inventory application for a company that sells widgets and gadgets. When a customer makes a purchase, the item can either be available, in which case the item is removed from inventory and the number of items left is returned, or it might be out of stock. Now, being out of stock is a perfectly normal thing to happen in an inventory application. It is certainly not an exceptional circumstance. But what do we return if it's out of stock? A string saying out of stock? A negative number? In both cases, the calling method would have to check whether the return value is a positive integer or something else, to determine if it is out of stock. That seems a bit messy, especially if we forget to do it somewhere in our code.

Using exceptions for flow control can make for some handy program designs. The important thing to take from this discussion is that exceptions are not a bad thing that we should try to avoid. Having an exception occur does not mean that you should have prevented this exceptional circumstance from happening. Rather, it is just a powerful way to communicate information between two sections of code that may not be directly calling each other.
