# PowershellClassesandconcepts

# Introduction
https://xainey.github.io/2016/powershell-classes-and-concepts/

# Syntax Overview
## Class Structure

The following is an overview of Object-Oriented Programming and Class syntax in WMF5.

#### Classes

We can think of ````Classes```` as models or blueprints.

````powershell

# Class Syntax

class CyberNinja

{

}

````

To use a Class, we create a special *Type* of variable known as an **Object**; an *Object* is an **instance** of a Class. Similarly, this is the same idea where a contractor can use a blueprint to build multiple houses. Once created, an object has access to **properties** and **methods** defined by its class.

#### Properties

````Properties```` are a special type of class member which define a **field** (data variable) as well as hidden methods to *get* and *set* the value.

 

A **Property** is composed of a ````data type````, ````name````, ````default value````, ````access modifier````, and ````non-access modifier````.

 

* **Data Type**: A data type can be a built-in type like *[String]*, *[Int32]*, *[Bool]*.

  * Additionally, this could also be a custom data type such as another PowerShell class: ````[CyberNinja]````.

 

* **Name**: The name of the property.

  * The name follows a set of allowed rules such as alphanumeric, underscores, dashes, and, numbers.

 

* **Default Value (Optional)**: Specifies the default value of a Property when creating an object.

  * If a value is not declared, the property will not always be null.

  * The default value of the properties data type determines the value.

 

* **Access Modifier (Optional)**: *[public, hidden]*. The default modifier is public; however, we do not use the public keyword.

 

* **Non-access Modifier (Optional)**: The *static* keyword controls if a property is an instance or class/static type.

 

````PowerShell

# Public Properties

[String] $Alias

[Int32] $HitPoints

 

# Static Properties

static [String] $Clan = "Posh Shinobi"

 

# Hidden Properties

hidden [String] $RealName

````

 

> Note: More on Access Modifiers below.

 

#### Methods

 

The term ````Method```` is a fancy way of describing a function defined inside of a class. In OOP, a method can take arguments the same as a function; however, they must return a value.

 

* If a method **does not** return a value, the return type is ````[Void]````.

* A **data type** should be type-hinted for each argument in the method header, e.g., ````[String] $Name````.

 

A method should have an ````access modifier````, ````name````, ````arguments````, and ````return type````.

 

* The access modifier is considered **public** if left blank.

* If ````static```` is **not** declared the property will be an instance type.

 

````powershell

# Instance Method

[String] getAlias()

{

    return $this.Alias

}

 

# Static Method

static [String] getClan()

{

    return [CyberNinja]::Clan

}

 

# Static Method

static [String] Whisper ([String] $Name)

{

    return "Hello {0}!" -f $Name

}

````

 

#### $this

 

The ````$this```` variable describes the **current instance** of the object. It is thought of like ````$_```` for classes.

 

* If a property is not static, the syntax ````$this.PropertyName```` is used to reference the instance property.

* To refer to methods simply use the method name or ````$this.MethodName()````.

 

> Note: A static method cannot use $this.

 

#### Constructor

 

A ````Constructor```` is a type of method that is called only when an object is created.

 

* A constructor **must** use the same name as the class.

 

Let’s say we create a class called ````CyberNinja```` with properties for the ninja’s **Alias** and **HitPoints**. By design, we would not want to allow someone to create a ninja object without filling in the required properties. To force any required arguments, we need a constructor.

 

Constructors are similar to the ````Begin```` block in ````Functions````.

 

````PowerShell

class CyberNinja

{

    # Constructor

    CyberNinja ([String] $Alias, [int32] $HitPoints)

    {

        $this.Alias = $Alias

        $this.HitPoints = $HitPoints

    }

}

````

 

#### ToString

 

````ToString```` is one of the convenient object methods seen in traditional OOP. If an object is passed to a function which accepts a string argument, **ToString** will automatically be called.

 

* If ToString is not added/overwritten in the class, the default *ToString* method returns the class name.

* The default object behavior can be forced by casting the object to ````[System.Object]````.

 

````PowerShell

class myColor

{

    [String] $Color

    [String] $Hex

 

    myColor([String] $Color, [String] $Hex)

    {

        $this.Color = $Color

        $this.Hex = $Hex

    }

 

    [String] ToString()

    {

        return $this.Color + ":" + $this.Hex

    }

}

````

````

PS C:\> $red = [myColor]::new("Red", "#FF0000")

 

PS C:\> Write-Host $red

Red:#FF0000

 

PS C:\> Write-Host ([System.Object]$red).ToString()

myColor

````

 

#### Example 1: Class Structure

 

The following code is a simple example of a basic class. Next, we look at how to use a class and continue with some more advanced concepts.

 

````PowerShell

class CyberNinja

{

    # Properties

    [String] $Alias

    [int32] $HitPoints

 

    # Static Properties

    static [String] $Clan = "DevOps Library"

 

    # Hidden Properties

    hidden [String] $RealName

 

    # Parameterless Constructor

    CyberNinja ()

    {

    }

 

    # Constructor

    CyberNinja ([String] $Alias, [int32] $HitPoints)

    {

        $this.Alias = $Alias

        $this.HitPoints = $HitPoints

    }

 

    # Method

    [String] getAlias()

    {

       return $this.Alias

    }

 

    # Static Method

    static [String] getClan()

    {

        return [CyberNinja]::Clan

    }

 

    # ToString Method

    [String] ToString()

    {

        return $this.Alias + ":" + $this.HitPoints

    }

}

````

 

## Creating instances of a class

 

To use a class, we must **instantiate** an object unless using static properties or methods. Most commonly, this is done using the ````new()```` static method or the ````New-Object Command````. In some cases, such as creating classes dynamically by type, the *New-Object* command is necessary.

 

````PowerShell

# Using Static "new" method.

$Ken = [CyberNinja]::new("Ken", 28)

 

# Using New-Object. Parameters for Argument list are positional and required by the constructor.

$Hodge = New-Object CyberNinja -ArgumentList "Hodge", 31

 

# Using a HashTable. Note: requires default or parameterless constructor.

$June = [CyberNinja]@{

    Alias = "June";

    HitPoints = 40;

}

 

# Dynamic Object Type using a variable name.

$Type = "CyberNinja"

$Steven = New-Object -TypeName $Type

````

When considering code design, it is common to declare the object type explicitly.

 

````

PS C:\> [CyberNinja] $Ken = [CyberNinja]::new("Ken", 28)

````

 

## Static vs. Instance

 

The keyword ````static```` is a **non-access modifier** for properties and methods within a class.

 

* For **properties**, the value is the same across every instance of the class.

* For **methods**, the method cannot use instanced variables such as ````$this.Name````

 

Looking back at *Example 1*, if we create ten *CyberNinja* objects and change the **$Clan** property of any one of them, the change will be reflected in *every single Object*.

 

#### Static

 

* Uses the ````::```` operator to access the property or method.

* Typically called using ````[Class]::Property````, where “Class” is the name of the class.

 

````PowerShell

[CyberNinja] $Ken = [CyberNinja]::new("Ken", 28)

 

# Call a Static Method

$Ken::getClan()

[CyberNinja]::getClan()

 

# Fetch Static Prop Value

$Ken::Clan

[CyberNinja]::Clan

 

# Set Static Prop Value

$Ken::Clan = "DevOps Library"

[CyberNinja]::Clan = "DevOps Library"

````

 

#### Instance

 

* Uses the ````.```` operator to access the property or method.

* Must be called on an instanced object of a class – not directly.

 

````PowerShell

[CyberNinja] $Ken = [CyberNinja]::new("Ken", 28)

 

# Call an Instance Method

$Ken.getAlias()

 

# Fetch Instance Prop Value

$Ken.HitPoints

 

# Set Instance Prop Value

$Ken.Alias = "Mekuto"

````

 

## Accessors/Mutators aka Getter/Setter

 

Properties in a PowerShell class can be static, public, and hidden.

 

* By default, a property is public.

* To get a property value from an object, use $obj.Name.

* To set a property from an object, use $obj.Name = "New Name".

 

Powershell automatically creates accessor methods for getting and setting the value. These hidden accessor methods are called get_x, set_x where “x” is the property name.

 

> Hidden is not the same as private in languages such as C#.

 

If we create an object of the [CyberNinja] class and view the members with Get-Member, we can see all of the public members.

 

````

PS C:\> $Chris = [CyberNinja]::new("Mirishikiari", 28)

 

PS C:\> $Chris | Get-Member

 

PS C:\> $Chris | Get-Member

 

   TypeName: CyberNinja

 

Name        MemberType Definition

----        ---------- ----------

Equals      Method     bool Equals(System.Object obj)

GetHashCode Method     int GetHashCode()

getAlias    Method     string getAlias()

GetType     Method     type GetType()

ToString    Method     string ToString()

Alias       Property   string Alias {get;set;}

HitPoints   Property   int HitPoints {get;set;}

````

 

Hidden members can be viewed by supplying the -Force Flag to Get-Member.

 

> Note: V5 does not have private variables since PowerShell uses PowerShell for debugging.

 

In the following code example, notice the get_x and set_x methods PowerShell automatically creates for our properties. Our hidden RealName Property is also accessible – how quaint.

 

````

PS C:\> $Mike = [CyberNinja]::new("Xainey", 28)

 

PS C:\> $Mike | Get-Member -Force

 

   TypeName: CyberNinja

 

Name          MemberType   Definition                                

----          ----------   ----------                                 

pstypenames   CodeProperty System.Collections.ObjectModel.Collection`1...

psadapted     MemberSet    psadapted {Alias, HitPoints, get_Alias, set...

psbase        MemberSet    psbase {Alias, HitPoints, get_Alias, set_Al...

psextended    MemberSet    psextended {}                             

psobject      MemberSet    psobject {Members, Properties, Methods, Imm...

Equals        Method       bool Equals(System.Object obj)            

getAlias      Method       string getAlias()                         

GetHashCode   Method       int GetHashCode()                         

GetType       Method       type GetType()                             

get_Alias     Method       string get_Alias()                        

get_HitPoints Method       int get_HitPoints()                       

get_RealName  Method       string get_RealName()                     

set_Alias     Method       void set_Alias(string )                   

set_HitPoints Method       void set_HitPoints(int )                  

set_RealName  Method       void set_RealName(string )                

ToString      Method       string ToString()                          

Alias         Property     string Alias {get;set;}                   

HitPoints     Property     int HitPoints {get;set;}                  

RealName      Property     string RealName {get;set;}                

````

 

> Hidden properties are masked, but still accessible to the debugger.

> **June Blender - @juneb**

 

Get-Member can be useful for finding methods on custom objects and built-in objects.

 

````PowerShell

# All

[Math] | Get-Member

 

# Static Only Methods

[Math] | Get-Member -Static

 

# Hidden

[Math] | Get-Member -Force

````

 

## Overloaded Methods

 

````Method Overloading```` is a way to define multiple methods with the same name. Overloaded methods behave differently depending on **the number of arguments** or the **data types of the arguments** supplied. In the following code example, ````SayHello()```` and ````add()```` can be called different ways.

 

````PowerShell

class OverloadExample

{

    static [String] SayHello ()

    {

        return "Hello There!"

    }

 

    static [String] SayHello ([String] $Name)

    {

        return "Hello {0}!" -f $Name

    }

 

    static [int] add([int] $a, [int] $b)

    {

        return $a + $b

    }

 

    static [double] add([double] $a, [double] $b)

    {

        return $a + $b

    }

}

````

 

````

PS C:\> [OverloadExample]::SayHello()

Hello There!

 

PS C:\> [OverloadExample]::SayHello("Mike")

Hello Mike!

 

PS C:\> [OverloadExample]::add(1, 2)

3

 

PS C:\> [OverloadExample]::add(1.1, 2.3)

3.4

````

 

> Methods and Constructors are overloadable.

 

We could also refactor the above example to simplify ````SayHello()````. In this next snippet, the parameterless ````SayHello()```` method is funneled through the single argument method.

 

````PowerShell

class OverloadRefactor

{

    # Calls Overloaded Method

    static [String] SayHello ()

    {

        return [OverloadRefactor]::SayHello("There")

    }

 

    static [String] SayHello ([String] $Name)

    {

        return "Hello {0}!" -f $Name

    }

}

````

 

````

PS C:\> [OverloadRefactor]::SayHello()

Hello There!

 

PS C:\> [OverloadRefactor]::SayHello("Mike")

Hello Mike!

````

 

## Inheritance

 

````Inheritance```` allows for programmers to create classes from existing classes by **extending** them. In this way, we can reuse classes and extend the functionality without editing a closed class. \*cough SOLID\* When a class is **extended**, all of the members from the base or parent class are inherited (passed on) to the child class.

 

* To extend a class, use the syntax ````Class Child : Parent````.

 

````PowerShell

# Foo is the parent class

class Foo

{

    [string] $Message = "Hello!"

 

    [string] GetMessage()

    {

        return ("Message: {0}" -f $this.Message)

    }

}

 

# Bar extends Foo and inherits its members

class Bar : Foo

{

 

}

````

 

The class **Bar** does not declare any properties or methods. If we create an instance of the **Bar** class, it will **inherit** all of the members of its parent class.

 

````

PS C:\> $myBar = [Bar]::new()

 

PS C:\> $myBar.Message

Hello!

 

PS C:\> $myBar.GetMessage()

Message: Hello!

````

 

> Note: To override this behavior, we must **redeclare** the members in the child class.

 

## Using the Base Constructor

 

A child class can call the constructor of its parent by using the ````: base()```` command on its constructor.

 

````PowerShell

class ZeroWing

{

    [String] $User

    [String] $Message

 

    ZeroWing([String] $User, [String] $Message)

    {

        $this.User = $User

        $this.Message = $Message

    }

 

    [String] TurnOn()

    {

        return ("{0} : {1}" -f $this.User, $this.Message)

    }

}

 

class MainScreen : ZeroWing

{

    MainScreen([String] $User, [String] $Message) : base($User, $Message)

    {

 

    }

}

````

 

The constructor for ````MainScreen```` **maps** parameters to the base constructor.

 

````

PS C:\> $mainScreen = [MainScreen]::new("CATS", "All your base are belong to us.")

 

PS C:\> $mainScreen.User

CATS

 

PS C:\> $mainScreen.TurnOn()

CATS : All your base are belong to us.

````

 

## Enumerations

 

An `````Enum````` is a special *Type* which defines a set of named constants. In PowerShell, we can use an **Enum** as an argument type for a method in a *Class*. The Enum type lets a method **restrict** the argument values it can accept.

 

````PowerShell

Enum Turtles

{

    Donatello

    Leonardo

    Michelangelo

    Raphael

}

````

 

````PowerShell

Enum Turtles

{

    Donatello = 1

    Leonardo = 2

    Michelangelo = 3

    Raphael = 4

}

````

 

````

To get names from an enum

PS C:\> [System.Enum]::GetValues([Turtles])

 

To get int values from an enum

PS C:\> [System.Enum]::GetValues([Turtles]) | foreach { [int] $_ }

````

 

# Design Patterns

## Polymorphism

## Abstract Classes

## Singleton

## Factory Pattern

#### Standard Factory Object Generation

#### Using Static Methods to Set/Fetch Objects

## Method Chaining

## Base Splat Pattern

#### Using object properties to Splat a function

#### Using Base Helper to Generate Splat HashTable

## Loading Class Files

#### Possible Workarounds

# Conclusion
