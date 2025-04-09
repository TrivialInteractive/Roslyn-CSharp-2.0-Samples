# Compile And Load From C# Project (.csproj)

This example shows how you can compile and load a C# project file (.csproj) using Roslyn. C# Compile and load operations mean that the source will first be compiled, then code security checked if applicable, and then finally loaded into the game so it can be executed when required.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using RoslynCSharp.Project;
using Trivial.CodeSecurity;
using UnityEngine;
```
</details>

---

#### Compile And Load Project
You can compile and load a C# project as shown in this example

```cs
public class CompileAndLoadProject : MonoBehaviour
{
    // Private
    private const string projectFile = "path/to/project/file.csproj";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Parse the C# project
        // Parsing supports loading the assembly name, source files, define symbols and reference paths
        CSharpProject project = CSharpProject.Parse(projectFile);

        // Now we can make a compile request for the C# project
        // Note that this will compile, security check and load the resulting assembly into the game if successful. 
        ScriptAssembly assembly = domain.CompileAndLoadProject(project);

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

#### Compile And Load Project Report
In many cases the above example will work just fine, however there are overloads available that provide detailed information about the compilation result, as well as the code security validation result. It may be useful when you need to report failures using custom logging, or just would like to know at which stage the operation failed:

```cs
public class CompileAndLoadProject : MonoBehaviour
{
    // Private
    private const string projectFile = "path/to/project/file.csproj";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Parse the C# project
        // Parsing supports loading the assembly name, source files, define symbols and reference paths
        CSharpProject project = CSharpProject.Parse(projectFile);

        // Now we can make a compile request for the C# project
        // Note that this will compile, security check and load the resulting assembly into the game if successful.
        CompileResult compileResult;
        CodeSecurityReport securityReport;
        ScriptAssembly assembly = domain.CompileAndLoadProject(project, out compileResult, out securityReport);

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
