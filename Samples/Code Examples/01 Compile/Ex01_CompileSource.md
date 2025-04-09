
# Compile From Source
This example shows how you can compile one or more strings containing C# source code using Roslyn C#. 
Note that this example only covers compiling and will not load the resulting output assembly into the game. For most users it is recommended to check out the `CompileAndLoadSource` example which covers compilation and loading in a single operation.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using System.Linq;
using UnityEngine;
```
</details>

---

#### Compile Source String
Firstly a string containing C# code can be compiled as shown in this example:
```cs
public class CompileSource : MonoBehaviour
{
    // Private
    private const string sourceCode = @"
    using UnityEngine;
    class Example
    {
        static void ExampleMethod()
        {
            Debug.Log(""Hello World"");
        }
    }";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the source C# text
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadSource' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileSource(sourceCode);

        // Check if the compile was successful
        if(compileResult.Success == false)
        {
            // Compile has failed - errors will be logged in the console by default, but can also be accessed from the result
            // Here we get only errors but the compiler may also provide warnings and messages.
            CompileError[] errorsOnly = compileResult.Errors
                .Where(e => e.IsError)
                .ToArray();
        }
    }
}
```

#### Compile Source String Array
Additionally it is also posisble to compile multiple strings together in a batch, and that way the code contained within the strings will be able to reference each other as if they were part of the same C# project.
Note that the method name has changed to the plural `CompileSource(s)`:
```cs
public class CompileSource : MonoBehaviour
{
    // Private
    private const string sourceCode = @"
    using UnityEngine;
    class Example
    {
        static void ExampleMethod()
        {
            Debug.Log(""Hello World"");
        }
    }";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the source C# text (Note the name change - CompileSource`s` plural)
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadSource' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileSources(new string[]    
        {
          // Note that only 1 string is added here for this example, but we can provide as many as required
          sourceCode,
        );

        // Check if the compile was successful
        if(compileResult.Success == false)
        {
            // Compile has failed - errors will be logged in the console by default, but can also be accessed from the result
            // Here we get only errors but the compiler may also provide warnings and messages.
            CompileError[] errorsOnly = compileResult.Errors
                .Where(e => e.IsError)
                .ToArray();
        }
    }
}
```
