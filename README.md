# Assignment-1-overleaf-templates-

\documentclass[12pt,
	english,
	brazil,
	]{article}

%% Language and font encodings
\usepackage[english]{babel}
\usepackage[utf8x]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{indentfirst}
\usepackage{float}

%% Sets page size and margins
\usepackage[a4paper,top=3cm,bottom=2cm,left=3cm,right=3cm,marginparwidth=1.75cm]{geometry}

%% Useful packages
\usepackage{amsmath}
\usepackage{graphicx}
\usepackage[colorinlistoftodos]{todonotes}
\usepackage[colorlinks=true, allcolors=blue]{hyperref}
\usepackage{url}
\usepackage{listings}
\usepackage{xcolor}

%% Java code style
\lstdefinestyle{javastyle}{
    language=Java,
    basicstyle=\ttfamily\small,
    keywordstyle=\color{blue}\bfseries,
    stringstyle=\color{red},
    commentstyle=\color{gray}\itshape,
    numberstyle=\tiny\color{gray},
    numbers=left,
    stepnumber=1,
    breaklines=true,
    frame=single,
    rulecolor=\color{black!30},
    backgroundcolor=\color{gray!5},
    tabsize=4,
    showstringspaces=false,
    captionpos=b
}
\lstset{style=javastyle}

%% Document Header
\title{ Object Oriented Pattern and Design (Java)\\Course Code: ICT 2207

\Large{OOP Basics --- Assignment Answers}\\
\large{Mawlana Bhashani Science and Technology University (MBSTU)\\Department of Information and Communication Technology}}


\author{ SUBMITTED BY:\\
Md. Jahidul Islam\\
ID: IT24018 ;    Session: 2023-24\\
Year: Second  Semester: Second\\
Department of ICT, MBSTU}

\date{\ 25 June 2026}

%% Document Begin
\begin{document}
\maketitle

\begin{abstract}
This document presents the answers to the five long-answer/practical questions (Section C) of the ICT 2207 — Object Oriented Pattern Design (Java) assessment booklet. The topics covered include static members and constructor overloading/chaining, abstract class vs.\ interface, method overriding vs.\ overloading, the Object class and \texttt{toString()}, and polymorphism with dynamic binding. Each answer combines a conceptual explanation with a working Java code example.
\end{abstract}

\tableofcontents
\newpage

% ============================================================
\section{Q1. Static Members \& Constructor Overloading/Chaining (Understand + Apply)}
% ============================================================

\subsection*{(a) Static Variable vs.\ Instance Variable}

A \textbf{static variable} (also called a class variable) is shared among all instances of a class. It is allocated once in the method area of the JVM when the class is loaded, and its scope is the entire class. Any change made by one object is visible to all other objects.

An \textbf{instance variable}, in contrast, belongs to an individual object. A separate copy is created in the heap for every new object, and its scope is limited to that particular object.

\textbf{Why a static method cannot directly access an instance variable:}\\
A static method belongs to the class, not to any particular object, so it can be called without creating an instance (e.g., \texttt{ClassName.method()}). At the time of the call there may be zero, one, or many objects; there is no implicit \texttt{this} reference. Because instance variables only exist inside an object, a static method has no way of knowing \emph{which} object's variable to access, and the compiler therefore disallows the direct access.

\subsection*{(b) Employee Class with Constructor Chaining}

\begin{lstlisting}[caption={Employee.java --- static counter with chained constructors}]
public class Employee {

    // Instance fields
    private int    id;
    private String name;
    private double salary;

    // Static variable shared by ALL Employee objects
    private static int count = 0;

    //  Constructor 
    public Employee() {
        this("Unknown", 0.0);   // delegate to Employee(String, double)
    }

    //  Constructor 
    public Employee(String name) {
        this(name, 30000.0);    // delegate with default salary
    }

    //  Constructor 
    public Employee(String name, double salary) {
        this.id     = ++count;  // count incremented EXACTLY ONCE per object
        this.name   = name;
        this.salary = salary;
    }

    // Static factory method to retrieve count
    public static int getCount() {
        return count;
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name +
               "', salary=" + salary + "}";
    }

    // Test 
    public static void main(String[] args) {
        Employee e1 = new Employee();                  
        Employee e2 = new Employee("Alice");           
        Employee e3 = new Employee("Bob", 55000.0);    

        System.out.println(e1);
        System.out.println(e2);
        System.out.println(e3);
        System.out.println("Total Employees: " + Employee.getCount());
    }
}
\end{lstlisting}

\textbf{Expected Output:}
\begin{verbatim}
Employee{id=1, name='Unknown', salary=0.0}
Employee{id=2, name='Alice', salary=50000.0}
Employee{id=3, name='Bob', salary=85000.0}
Total Employees: 4
\end{verbatim}

\textbf{Key points:}
\begin{itemize}
    \item \texttt{this(...)} on the first line of a constructor delegates to another constructor in the same class.
    \item The increment \texttt{++count} appears only in the master constructor (Constructor 3), so it runs exactly once per object regardless of which constructor the caller uses.
    \item \texttt{getCount()} is \texttt{static} because it only reads a static variable and needs no object reference.
\end{itemize}

\newpage
% ============================================================
\section{Q2. Abstract Class vs.\ Interface (Understand + Apply)}
% ============================================================

\subsection*{(a) Three Differences and Design Scenarios}

\begin{table}[H]
\centering
\begin{tabular}{|l|l|l|}
\hline
\textbf{Feature} & \textbf{Abstract Class} & \textbf{Interface} \\
\hline
Constructors & Can have constructors & Cannot have constructors \\
\hline
Fields & Can have instance fields & Only \texttt{public static final} constants \\
\hline
Multiple Inheritance & A class extends only one & A class can implement many \\
\hline
Method Bodies & Can have concrete methods & \texttt{default}/\texttt{static} since Java 8 only \\
\hline
\end{tabular}
\caption{Key differences between abstract class and interface}
\end{table}

\textbf{When to choose an abstract class:}\\
If you are designing a \textit{Vehicle} hierarchy where \texttt{Car} and \texttt{Truck} share common state (\texttt{fuelLevel}) and partially implemented behaviour (\texttt{refuel()}), use an abstract class. The shared state cannot live in an interface (no instance fields), and forcing every subclass to re-implement \texttt{refuel()} would duplicate code.

\textbf{When to choose an interface:}\\
If you want to add a \textit{Flyable} capability to objects as diverse as \texttt{Bird}, \texttt{Drone}, and \texttt{AirCraft} (which already extend different class hierarchies), use an interface. Each class can implement \texttt{Flyable} independently while still extending its own parent, something impossible with a single abstract class due to Java's single-inheritance rule.

\subsection*{(b) Class/Interface Hierarchy for Car, Bicycle, and Boat}

\begin{lstlisting}[caption={Movable interface and MotorVehicle abstract class skeleton}]
// ALL three support move() 
interface Movable {
    void move();
}

// Car and Boat share fuelLevel state and refuel() behaviour
abstract class MotorVehicle implements Movable {
    protected double fuelLevel;

    public void refuel(double amount) {
        fuelLevel += amount;
    }

}

class Car extends MotorVehicle {
    @Override
    public void move() { /* drive on road */ }
}


class Boat extends MotorVehicle {
    @Override
    public void move() { /* sail on water */ }
}

class Bicycle implements Movable {
    @Override
    public void move() { /* pedal */ }
}
\end{lstlisting}

\newpage
% ============================================================
\section{Q3. Overriding vs.\ Overloading (Analyze)}
% ============================================================

\subsection*{(a) Four-Dimensional Comparison}

\begin{table}[H]
\centering
\begin{tabular}{|l|l|l|}
\hline
\textbf{Dimension} & \textbf{Overloading} & \textbf{Overriding} \\
\hline
Method Signature & Same name, different parameters & Identical name \emph{and} parameters \\
\hline
Inheritance Required & No (can occur in one class) & Yes (subclass overrides parent) \\
\hline
Binding Type & Compile-time (static) & Runtime (dynamic) \\
\hline
Single-Class Possible & Yes & No \\
\hline
\end{tabular}
\caption{Overloading vs.\ Overriding across four dimensions}
\end{table}

\subsection*{(b) Code Trace and Output Prediction}

\begin{lstlisting}[caption={Animal/Dog -- overloading and overriding demo}]
class Animal {
    void sound() { System.out.println("Some sound"); }
    void sound(int times) { System.out.println("Repeated " + times + " times"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}

public class Test {
    public static void main(String[] args) {
        Animal a = new Dog();
        a.sound();      // Line A
        a.sound(3);     // Line B
    }
}
\end{lstlisting}

\textbf{Analysis:}
\begin{itemize}
    \item \textbf{Line A --- \texttt{a.sound()}:}\\
          The compiler sees reference type \texttt{Animal} and resolves the call to \texttt{Animal.sound()} (no-arg). At runtime the actual object is a \texttt{Dog}, so dynamic binding kicks in and executes \texttt{Dog.sound()} --- \textbf{overriding}.\\
          Output: \texttt{Bark}

    \item \textbf{Line B --- \texttt{a.sound(3)}:}\\
          The compiler matches the call to \texttt{Animal.sound(int)} because \texttt{Dog} does not override the \texttt{int}-parameter version. This resolution happens purely at compile time based on the argument type --- \textbf{overloading}.\\
          Output: \texttt{Repeated 3 times}
\end{itemize}

\textbf{Full Expected Output:}
\begin{verbatim}
Bark
Repeated 3 times
\end{verbatim}

\newpage
% ============================================================
\section{Q4. The Object Class and \texttt{toString()} (Understand + Apply)}
% ============================================================

\subsection*{(a) Three Inherited Methods from Object}

Every Java class implicitly extends \texttt{java.lang.Object}. Three important inherited methods are:

\begin{enumerate}
    \item \textbf{\texttt{toString()}} --- Returns a string representation of the object. The default implementation returns \texttt{ClassName@hashCode} (e.g., \texttt{Book@1b6d3586}).
    \item \textbf{\texttt{equals(Object obj)}} --- Tests reference equality by default (\texttt{==}), i.e., whether two references point to the same memory address.
    \item \textbf{\texttt{hashCode()}} --- Returns an integer hash code. By default it is typically derived from the object's memory address.
\end{enumerate}

\textbf{Default \texttt{toString()} format:}\\
\texttt{getClass().getName() + '@' + Integer.toHexString(hashCode())}\\
Example: \texttt{Book@6d06d69c}

\subsection*{(b) Predicting Output and Overriding \texttt{toString()}}

\textbf{Before override:} \texttt{System.out.println(b1)} implicitly calls \texttt{b1.toString()}, which uses the default \texttt{Object} implementation. The output will be something like:
\begin{verbatim}
Book@<hexHashCode>   (e.g., Book@6d06d69c)
\end{verbatim}

\begin{lstlisting}[caption={Book.java with overridden toString()}]
class Book {
    String title;
    String author;
    double price;

    Book(String title, String author, double price) {
        this.title  = title;
        this.author = author;
        this.price  = price;
    }

    @Override
    public String toString() {
        return "Book: " + title + " by " + author + " (Price: " + price + ")";
    }
}

public class Test {
    public static void main(String[] args) {
        Book b1 = new Book("Java Basics", "Alice", 450.0);
        System.out.println(b1);   // implicitly calls b1.toString()
    }
}
\end{lstlisting}

\textbf{Output after override:}
\begin{verbatim}
Book: Java Basics by Alice (Price: 450.0)
\end{verbatim}

\textbf{Why \texttt{toString()} is preferred over a separate \texttt{display()} method:}
\begin{itemize}
    \item \texttt{System.out.println(obj)}, string concatenation (\texttt{"Info: " + obj}), and logging frameworks all call \texttt{toString()} automatically.
    \item A custom \texttt{display()} method requires explicit invocation and is invisible to the Java API.
    \item Overriding \texttt{toString()} is a standard Java convention, making the code more readable and compatible with libraries (e.g., collections print neatly with \texttt{toString()}).
\end{itemize}

\newpage
% ============================================================
\section{Q5. Polymorphism \& Dynamic Binding (Analyze + Evaluate)}
% ============================================================

\subsection*{(a) Compile-Time vs.\ Runtime Polymorphism}

\textbf{Compile-time (static) polymorphism} is resolved by the compiler based on the method signature. It is achieved through \textit{method overloading}. Example:
\begin{lstlisting}[caption={Compile-time polymorphism via overloading}]
class MathUtils {
    int add(int a, int b)       { return a + b; }
    double add(double a, double b) { return a + b; }
}
\end{lstlisting}
The compiler selects the correct \texttt{add} at compile time based on argument types.

\textbf{Runtime (dynamic) polymorphism} is resolved at runtime based on the actual object type. It is achieved through \textit{method overriding} and a parent-type reference. Example:
\begin{lstlisting}[caption={Runtime polymorphism via overriding}]
Animal a = new Dog();
a.sound();  // resolved at runtime -- calls Dog.sound(), not Animal.sound()
\end{lstlisting}

\textbf{Dynamic Binding} is the mechanism the JVM uses to determine \emph{at runtime} which overridden method to execute, by looking up the actual type of the object (not the declared reference type). Runtime polymorphism relies on dynamic binding; compile-time polymorphism does not.

\subsection*{(b) Code Trace --- Shape, Circle, Square}

\begin{lstlisting}[caption={Shape hierarchy -- dynamic binding trace}]
class Shape {
    double area() { return 0; }
    void describe() { System.out.println("Area: " + area()); }
}

class Circle extends Shape {
    double radius = 5;
    @Override
    double area() { return Math.PI * radius * radius; }
}

class Square extends Shape {
    double side = 4;
    @Override
    double area() { return side * side; }
}

public class Test {
    public static void main(String[] args) {
        Shape[] shapes = { new Circle(), new Square() };
        for (Shape s : shapes) {
            s.describe();
        }
    }
}
\end{lstlisting}

\textbf{Step-by-step trace:}

\begin{enumerate}
    \item \textbf{Iteration 1 --- \texttt{s} refers to a \texttt{Circle} object}
    \begin{itemize}
        \item \texttt{s.describe()} is called. At compile time, the compiler sees reference type \texttt{Shape}, so it binds to \texttt{Shape.describe()}.
        \item At runtime, the actual object is \texttt{Circle}, so \texttt{Shape.describe()} executes --- but inside \texttt{describe()}, \texttt{area()} is called.
        \item The call to \texttt{area()} is also dynamically bound: the actual object is \texttt{Circle}, so \texttt{Circle.area()} executes.
        \item \texttt{Circle.area()} returns $\pi \times 5^2 = \pi \times 25 \approx 78.53981633974483$.
        \item Output: \texttt{Area: 78.53981633974483}
    \end{itemize}

    \item \textbf{Iteration 2 --- \texttt{s} refers to a \texttt{Square} object}
    \begin{itemize}
        \item \texttt{s.describe()} calls \texttt{Shape.describe()} (compile-time reference type = \texttt{Shape}).
        \item Inside \texttt{describe()}, \texttt{area()} is dynamically bound to \texttt{Square.area()}.
        \item \texttt{Square.area()} returns $4 \times 4 = 16.0$.
        \item Output: \texttt{Area: 16.0}
    \end{itemize}
\end{enumerate}

\textbf{Full Expected Output:}
\begin{verbatim}
Area: 78.53981633974483
Area: 16.0
\end{verbatim}

\textbf{Key insight:} Even though \texttt{describe()} is defined in \texttt{Shape} and is NOT overridden in \texttt{Circle} or \texttt{Square}, the call to \texttt{area()} \emph{inside} \texttt{describe()} is still dynamically dispatched. This is because method calls in Java always use dynamic binding for virtual (non-static, non-private) methods, regardless of where the call originates.

\end{document}
