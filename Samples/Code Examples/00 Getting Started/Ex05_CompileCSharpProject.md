# Compile From C# Project (.csproj)
Roslyn C# supports basic parsing of `.csproj` files to fetch information such as assembly name, source files, define symbols and references. As a result, Roslyn C# also exposes compile API's that accept a `CSharpProject` object which is used to represent a loaded .csproj file.

This example shows how you can parse and compile an existing C# project file using Rolsyn C#. Note that this example only covers compiling and will not load the resulting output assembly into the game. For most users it is recommended to check out the CompileAndLoadProject example which covers compilation and loading in a single operation.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using RoslynCSharp.Project;
using System.Linq;
using UnityEngine;
```
</details>

---

#### Compile project
An existing C# project file can be parsed and compiled as shown in this example:
```cs
public class CompileCSharpProject : MonoBehaviour
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
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadProject' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileProject(project);

        // Check if the compile was successful
        if (compileResult.Success == false)
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
