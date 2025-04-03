# Compile And Load From File

This example shows how you can compile and load one or more strings containing C# source file paths using Roslyn C#.
Compile and load operations mean that the source will first be compiled, then code security checked if applicable, and then finally loaded into the game so it can be executed when required.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using Trivial.CodeSecurity;
using UnityEngine;
```
</details>

---

#### Compile And Load File
Firstly you can compile and load a source string containg C# code as shown below.

```cs
public class CompileAndLoadSource : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the source C# file
        // Note that this compile, security check and load the resulting assembly into the game if successful. 
        ScriptAssembly assembly = domain.CompileAndLoadFile(sourceFile);

        // Check if compile and load was successful
        // If compilation or code security verification failed, then the assembly will be null and details will be logged in the Unity console by default
        if(assembly != null)
        {
            // Log the name in the console
            // The script assembly can be used to access types by name, base type and other criteria. See later examples for more info
            Debug.Log(assembly.Name);
        }
    }
}
```

#### Compile And Load File Report
In many cases the above example will work just fine, however there are overloads available that provide detailed information about the compilation result, as well as the code security validation result.
It may be useful when you need to report failures using custom logging, or just would like to know at which stage the operation failed:

```cs
public class CompileAndLoadSource : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the source C# file
        // Note that this compile, security check and load the resulting assembly into the game if successful.
        CompileResult compileResult;
        CodeSecurityReport securityReport;
        ScriptAssembly assembly = domain.CompileAndLoadFile(sourceFile, out compileResult, out securityReport);

        // Check if compile and load was successful
        // If compilation or code security verification failed, then the assembly will be null and details will be logged in the Unity console by default
        if(assembly != null)
        {
            // Log the name in the console
            // The script assembly can be used to access types by name, base type and other criteria. See later examples for more info
            Debug.Log(assembly.Name);
        }
        else
        {
            // The compilation failed and as in previous exmaples we can get all diagnostic messages from the compile result object
            if(compileResult.Success == false)
            {
                Debug.Log("Compilation Failed!");
            }
            // The code security verification stage has failed (Assembly includes code which is determined to be illegal by the code seuciryt verification rules) and we can get detailed information about the specific from the security report.
            else if(securityReport.IsSecurityVerified == false)
            {
                Debug.Log("Code security verification failed!");
            }
        }
    }
}
```

# Compile Multiple
As in previous examples, there are also plural versions of these methods available for use when you need to compile multiple C# source files as a batch:
```cs
// Compile and load multiple C# source file paths
ScriptAssembly assembly = domain.CompileAndLoadFiles(new string[] { pathA, pathB });

// Compile and load multiple C# source file paths and get detailed information about the result
CompileResult compileResult;
CodeSecurityReport securityReport;
ScriptAssembly assembly = domain.CompileAndLoadFiles(new string[] { pathA, pathB }, out compileResult, out securityReport);
```
