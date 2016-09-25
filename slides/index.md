- title : Session#
- description : Session types for .Net
- author : Fahd Abdeljallal
- theme : beige 
- transition : default



<img width="250" src="images/logo.png">

## Session types for .NET

> **By** Fahd Abdeljallal

***

### Table of Contents

* [Session Types](#session)
* [Background](#generation)
    * [Code Generation](#generation)
    * [Type Provider](#provider)
* [Project contributions](#contribution)
* [Example](#fibonacci)
* [Supported features](#operator)
* [Communication model based on Actors](#communication)
* [Multi-Stage programming](#msp)
* [Case Study: Session# vs suave.io](#HTTP)
* [Conclusion](#conclusion)

***

### Session types -- Overview <a name="session"></a>

* Codify the structure of communication
* Avoid communication errors such as deadlocks, communication mismatches 

<img width="400" src="images/st.gif">

***

### Background -- Code Generation <a name="generation"></a>

<!-- <img src="images/Java.png" width="25%" style="float:left;"/> -->
<div style="float: right;" width ="50%">
    <ul>
    <li>The adoption of session types in
         mainstream programming languages is challenging </li>     
    <li> Hybrid session verification though endpoint API generation [FASE 2016] </li>
        <ul>
        <li> Only supported on JAVA </li>     
        </ul>
    <li> Disadvantages of code generation </li>
        <ul>
        <li> Maintanability </li>     
        <li> Increases the overall project size and complexity </li>     
        <li> The generated types can be easily tempered </li>     
        </ul>     
    </ul>

</div>

***

### Background -- Code Generation 

<center>
<img  src="images/ray.png" width="30%"/>
</center>

***

### Background -- Type Provider <a name="provider"></a>

    type Test = JsonProvider<""" { "name" : "Fahd" ,"age" : "22" } """>
    let test = Test.Parse(""" { "name" : "Khalissa" , "age" : "1" } """)
    let age = test.Age // -> 1
    let name = test.Name // -> Khalissa


* Generate types into the compiler instead of providing them in the source code
* Provides types ***On-Demand***
* Using Meta-information as a schema

***

### Session# to the rescue <a name="rescue"></a>

> **Type safe communication library for F# and .NET** 

<img width="800" src="images/Design.png">

***

### Contributions <a name="contribution"></a>

- Communication library for session types
- On-demand type generation from Scribble protocols
    - 0 lines of code generated 
- Session type support on all .NET languages
- Advanced features
    - Asynchronous and Synchronous workflows
    - Multiparty session types

***

### Challenges

- Type providers are used to generate data (and not functionality)
    - We want to generate types and code
- Integration with all .NET languages.
- No Prior knowledge of
    - F# and Types providers
    - Session types and Scribble
    - Actor model 

***
### Fibonacci Example <a name="fibonacci"></a>


    [lang=yaml]
    module demo;

    type <fsharp> "System.Int32" from "Nothing" as Int;

    global protocol Fibonacci(role A, role B){
        rec Fib {
            choice at A {
                fib(Int,Int) from A to B;
                fib(Int) from B to A;
                continue Fib;
            } or {
                bye() from A to B;
            }
        }
    }

***

<img width="500" src="images/fibo.png">

***

<img width="25%" src="images/fibo.png">
<img width="100%" src="images/intellisense.jpg">

***

### Session types primitives - Send and Receive <a name="operator"></a>
    


    [lang=yaml]
    fib(Int) from B to A;

<div align = "left">
     <ul>
        <li> Implementation for B </li>
    </ul>
</div>

    let c = new Provided.TypeProviderFile<"Scribble.scr",protocol,"B">() 
    
    let n = 5                            
    c.sendfib(A,n)

<div align = "left">
    <ul>
        <li> Implementation for A </li>
    </ul>
</div>

    
    let c = new Provided.TypeProviderFile<"Scribble.scr",protocol,"A">()
    
    let buf = new DomainModel.Buf<int>()       
    c.receivefib(B,buf)

***

### Session types primitives -- Selection

    [lang=yaml]
    choice at A {
        fib(Int,Int) from A to B;
        ...
    } or {
         bye() from A to B;
    }

Implementation for A

    let n = 5 , let m = 8
    match value with
        |0 -> c.sendbye(B)
        |n -> c.sendfib(B,n,m)

***

### Session types primitives -- Branching


    [lang=yaml]
    choice at A {
        fib(Int,Int) from A to B;
        ...
    } or {
         bye() from A to B;
    }

Implementation for B

    let buf1,buf2 = new DomainModel.Buf<int>()

    match c.branch() with
        | :? ByeChoice as bye -> bye.receive(A)
        | :? FibChoice as fib -> fib.receive(A,buf1,buf2)

***

### Demo-time <a name="demo"></a>


![alt text](images/logo.png "Session#")

***


### How is it done ?


<img width="1000" src="images/done.png">

***

### Communication Architecture <a name="communication"></a>

<img width="600" src="images/Actor.png">

***

### Multi-Stage Programming <a name="msp"></a>

<img width="560" src="images/msp.png">

***


##### Case Study (HTTP): Session# vs suave.io  <a name="HTTP"></a>

<section>
    <img width="350" src="images/logo.png">
    <center> VS </center>
    <img width="350" src="images/suave.gif">
</section>

***

### Conclusion and Future work <a name="conclusion"></a>

* An F# Library for :
    * Distributed programming
    * On-demand static generation types
    * Supporting Asynchronous + Synchronous workflows
    * Basic session types operators
    * ***.NET*** Types
* Actor Model

* Add support for Multi-thread programming
* Improve support for branching with lambdas
* Linearity at Compile-time

***
### We have been invited to give a talk

<img width="100%" src="images/meetup.jpg">

***

### Question & Answers

    type Question = TypeProvider<""" "Ask": "A question" """>
    let question = Question.Parse(""" "Ask" : "Session types rock" """)
    let response = question.Ask
***


