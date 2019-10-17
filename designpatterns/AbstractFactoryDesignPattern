concrete class : which implements all of its methods
abstract class: which may not implement all its methods

- Places where instantiation of concrete classes happens is the frequent change spot
- "factories" is a technique to encapsulate the behoviour of instantiation of concrete classes
- client depends on interface rather than concrete classes.
- program to an interface not an implementation makes code more flexible and expandable in future.
- *static method* cannot be a factory method as we can no longer extend it in  subclasses, it defeats factory pattern. But can be best used in *singleton* design pattern



**Factory Pattern**
- enables us to create an object without exposing the creation logic to the client and refer newly created object using a common interface.
- *creational pattern*
- also known as *virtual constructor*

Client handling instantiation directly:
without static factory method, any addition of implementation on the library must be reflected in client also. recompile again

Instantiation is done by library:
static factory method decides the instantiation depending on the type of object requested.
Adding new implementation needs changes to Factory method and not the client

```java
abstract class Department {
    public abstract Employee createEmployee(String id);//interface for creating objects but allows subclasses to alter the type of an object

    public fire(id) {
        $employee = this.createEmployee($id);
        $employee->paySalary();
        $employee->dismiss();
    }
}

class ITDepartment extends Department {
    public function createEmployee($id) {
        return new Programmer($id);
    }
}

class AccountingDepartment extends Department {
    public function createEmployee($id) {
        return new Accountant($id);
    }
}
```
*why?*
a. possibility of instantiation being complex - need some business logic to decide which instance to be created
b. polymorphism


**Abstract Factory Pattern**
 - creational pattern
 - solves the problem of creating entire product family without specifying their concerete class.
 AbstractFactory pattern: what it is, when to use it and how would you implement it?

 What it is:

It’s a design pattern user for object creation
this model allows us to create objects that follow a general pattern
an AbstractFactory provides an interface for creating families of related or dependent objects without specifying their concrete classes
 When to use it:

The client is independent of how we create and compose the objects in the system
The system consists of multiple families of objects, and these families are designed to be used together
We need a run-time value to construct a particular dependenc
 
