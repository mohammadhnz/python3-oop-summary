# When Objects Are Alike


## Multiple inheritance

The simplest and most useful form of multiple inheritance is called a mixin. A mixin is a superclass that is not intended to exist on its own, but is meant to be inherited by some other class to provide extra functionality.
```
class MailSender: 
	def send_mail(self, message):
		print("Sending mail to " + self.email)
		# Add e-mail logic here
class EmailableContact(Contact, MailSender):
	pass

```
## The diamond problem
Here, we have a base class that has a method named call_me. Two subclasses override that method, and then another subclass extends both of these using multiple inheritance. This is called diamond inheritance because of the diamond shape of the class diagram:

<img src="https://user-images.githubusercontent.com/59181719/114451010-f8e04800-9beb-11eb-85c3-fdc91c0eb0c3.png" width="450" height="500">



Let's convert this diagram to code; this example shows when the methods are called: 
```
class BaseClass: 
	num_base_calls = 0
	def call_me(self): 
		print("Calling method on Base Class")
		self.num_base_calls += 1 
class LeftSubclass(BaseClass):
	num_left_calls = 0
	def call_me(self):
		BaseClass.call_me(self)
		print("Calling method on Left Subclass")
		self.num_left_calls += 1 
class RightSubclass(BaseClass):
	num_right_calls = 0
	def call_me(self):
		BaseClass.call_me(self) 
		print("Calling method on Right Subclass") 
		self.num_right_calls += 1
class Subclass(LeftSubclass, RightSubclass):
	num_sub_calls = 0
	def call_me(self):
		LeftSubclass.call_me(self) 
		RightSubclass.call_me(self) 
		print("Calling method on Subclass") 
		self.num_sub_calls += 1
```
```
s = Subclass()
s.call_me() 
Calling method on Base Class 
Calling method on Left Subclass
Calling method on Base Class 
Calling method on Right Subclass 
Calling method on Subclass
```


we can clearly see the base class's call_me method being called twice. This could lead to some pernicious bugs if that method is doing actual work, such as depositing into a bank account, twice.

The thing to keep in mind with multiple inheritance is that we only want to call the next method in the class hierarchy, not the parent method. In fact, that next method may not be on a parent or ancestor of the current class. The super keyword comes to our rescue once again. Indeed, super was originally developed to make complicated forms of multiple inheritance possible.
```

class BaseClass:
	num_base_calls = 0
	def call_me(self):
		print("Calling method on Base Class")
		self.num_base_calls += 1 
		
class LeftSubclass(BaseClass): 
	num_left_calls = 0
	def call_me(self):
	super().call_me() 
	print("Calling method on Left Subclass")
	self.num_left_calls += 1
	
class RightSubclass(BaseClass):
	num_right_calls = 0
	def call_me(self):
		super().call_me() 
		print("Calling method on Right Subclass")
		self.num_right_calls += 1 
		
class Subclass(LeftSubclass, RightSubclass): 
	num_sub_calls = 0 
	def call_me(self): 
		super().call_me()
		print("Calling method on Subclass")
		self.num_sub_calls += 1
```
```
s = Subclass()
s.call_me() 
Calling method on Base Class 
Calling method on Right Subclass 
Calling method on Left Subclass 
Calling method on Subclass
```

First, call_me of Subclass calls super().call_me(), which happens to refer to LeftSubclass.call_me(). The LeftSubclass.call_me() method then calls super().call_me(), but in this case, super() is referring to RightSubclass.call_me(). 
Pay particular attention to this: the super call is not calling the method on the superclass of LeftSubclass (which is BaseClass). Rather, it is calling RightSubclass, even though it is not a direct parent of LeftSubclass! This is the next method, not the parent method. RightSubclass then calls BaseClass and the super calls have ensured each method in the class hierarchy is executed once.

## Polymorphism

We were introduced to polymorphism in Chapter 1, Object-Oriented Design. It is a showy name describing a simple concept: different behaviors happen depending on which subclass is being used, without having to explicitly know what the subclass actually is. As an example, imagine a program that plays audio files. A media player might need to load an AudioFile object and then play it. We can put a play() method on the object, which is responsible for decompressing or extracting the audio and routing it to the sound card and speakers. The act of playing an AudioFile could feasibly be as simple as:
```
audio_file.play()
```
However, the process of decompressing and extracting an audio file is very different for different types of files. While .wav files are stored uncompressed, .mp3, .wma, and .ogg files all utilize totally different compression algorithms.
#### Polymorphism or Duck typing
Polymorphism is actually one of the coolest things about object-oriented programming, and it makes some programming designs obvious that weren't possible in earlier paradigms. However, Python makes polymorphism seem less awesome because of duck typing. Duck typing in Python allows us to use any object that provides the required behavior without forcing it to be a subclass. The dynamic nature of Python makes this trivial.

Polymorphism is one of the most important reasons to use inheritance in many object- oriented contexts. Because any objects that supply the correct interface can be used interchangeably in Python, it reduces the need for polymorphic common superclasses. Inheritance can still be useful for sharing code, but if all that is being shared is the public interface, duck typing is all that is required. This reduced need for inheritance also reduces the need for multiple inheritance; often, when multiple inheritance appears to be a valid solution, we can just use duck typing to mimic one of the multiple superclasses.

Of course, just because an object satisfies a particular interface (by providing required methods or attributes) does not mean it will simply work in all situations. It has to fulfill that interface in a way that makes sense in the overall system.

Another useful feature of duck typing is that the duck-typed object only needs to provide those methods and attributes that are actually being accessed. For example, if we needed to create a fake file object to read data from, we can create a new object that has a read() method; we don't have to override the write method if the code that is going to interact with the fake object will not be calling it. More succinctly, duck typing doesn't need to provide the entire interface of an object that is available; it only needs to fulfill the interface that is actually accessed.

## Creating an abstract base class

```

import abc class
MediaLoader(metaclass=abc.ABCMeta):
	@abc.abstractmethod 
	def play(self):
		pass 
	@abc.abstractproperty
	def ext(self):
		pass 
	@classmethod 
	def __subclasshook__(cls, C):
		if cls is MediaLoader:
			attrs = set(dir(C)) 
			if set(cls.__abstractmethods__) <= attrs:
				return True
		return NotImplemented
```

The first weird thing is the metaclass keyword argument that is passed into the class where you would normally see the list of parent classes. This is a seldom-used construct from the mystic art of metaclass programming. We won't be covering metaclasses in this book, so all you need to know is that by assigning the ABCMeta metaclass, you are giving your class superhero (or at least superclass) abilities.


let's dissect that __subclasshook__ method. It is basically saying that any class that supplies concrete implementations of all the abstract attributes of this ABC should be considered a subclass of MediaLoader, even if it doesn't actually inherit from the MediaLoader class.

#### Demystifying the magic
```
def __subclasshook__(cls, C):
```
This defines the __subclasshook__ class method. This special method is called by the Python interpreter to answer the question: Is the class C a subclass of this class?
```
if set(cls.__abstractmethods__) <= attrs: 
```
This line uses set notation to see whether the set of abstract methods in this class has been supplied in the candidate class. Note that it doesn't check to see whether the methods have been implemented; just if they are there.

If any of the conditionals have not been met (that is, the class is not MediaLoader or not all abstract methods have been supplied), then return NotImplemented. This tells the Python machinery to use the default mechanism (does the candidate class explicitly extend this class?) for subclass detection.
