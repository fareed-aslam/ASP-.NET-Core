int age = 22;               // 4-byte whole number
string name = "Fareed";     // Reference type text
double salary = 25000.50;   // 8-byte floating point number
bool isActive = true;       // Boolean (true/false)

// Implicitly typed variables (Type is fixed at compile time by the compiler)
var score = 95; 

// Dynamic type (Bypasses compile-time checking; evaluated at runtime)
dynamic flexibleData = "Hello";
flexibleData = 100; // This is allowed

// Constants (Must be assigned at declaration; cannot change ever)
const double Pi = 3.14159;

// Readonly (Can be assigned at declaration or inside a class constructor)
readonly string ConfigPath = "C:/app/config.json";



using System;

public class HelloWorld
{
    public static void Main(string[] args)
    {
        // VAR...... used to store as the dynamics data type value
        Console.WriteLine ("Start small. Ship something.");
        var a="bro";
        Console.WriteLine($"value of a is: {a}");
        Console.WriteLine("value of a is {0}:",a);
        Console.WriteLine("value of a is: "+a);
        
        a="okay bhai";
        Console.WriteLine($"value of a is: {a}");
        // var should be not be passed to the function parameter 
        // also the var should not be returned from the function 
        // when we initialize var then must declare the value in it as the const required the declaration while initilazing
        // value of the var can not be changed to other data type once the data type is declared
        // intelisence help is available
        // var is value type variable
        
        // DYNAMIC....used to store as the dynamics data type value
        // dynamic is reference type
        
        //Readonly: same as const but value declaration is not must while initilazing it
        // also we can not use is in the funtion or the method scope it should be initialized in the class or as the member variables
        
        
        // all the points for the var just consider the opposite of those for the dynamic 
        
        
        
        
    }
}
