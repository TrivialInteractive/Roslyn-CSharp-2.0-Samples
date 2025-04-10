# Reference Compiled Code

Another common use case is to allow compiled code to reference other code that was compiled at an earlier time. It is similar to adding a C# project reference in an IDE.
Roslyn C# tries to make this as simple as possible by providing the `AssemblySource` object for all compile requests which implements the `ICompilationReference` interface.
Additionally the `ScriptAssembly` object returned by any compile and load method also implements the `ICompilationReference` interface, so can simply be passed as a reference object.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using UnityEngine;
```
</details>

---

# Reference Compile Request
Firstly if you just want to compile the inital code but not load it, this is made possible by using the `AssemblySource` of the compile result. 
```cs
public class ReferenceAssembly : MonoBehaviour
{
    // Private
    private const string sourcePath1 = "path/to/source1.cs";
    private const string sourcePath2 = "path/to/source2.cs";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Compile some code first
        CompileResult result = domain.CompileFile(sourcePath1);

        // Create compile options with additional references
        // We can use `FromSettings` here because we want to keep all options setup in the Roslyn C# settings, but need to add additional references
        CompileOptions options = CompileOptions.FromSettings();

        // Add our extra reference from the compile result
        // We can add as many as we like, but for this example one extra reference is added
        options.References.Add(result.Assembly);        

        // Now we can compile with our custom options, and the compiled code will be able to use public API's defined in the reference assembly
        CompileResult compileResult = domain.CompileFile(sourcePath2, options);
    }
}
```

# Reference Compile And Load Request
In most cases you will likely need to load the compiled assembly into the game so the code can be executed at some point. 
Again this is made quite simple since `ScriptAssembly` implements the `ICompilationReference` interface and can be passed directly as a reference.

```cs
public class ReferenceAssembly : MonoBehaviour
{
    // Private
    private const string sourcePath1 = "path/to/source1.cs";
    private const string sourcePath2 = "path/to/source2.cs";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Compile some code first
        ScriptAssembly assembly1 = domain.CompileAndLoadFile(sourcePath1);

        // Create compile options with additional references
        // We can use `FromSettings` here because we want to keep all options setup in the Roslyn C# settings, but need to add additional references
        CompileOptions options = CompileOptions.FromSettings();

        // Add our extra reference via the script assembly
        // We can add as many as we like, but for this example one extra reference is added
        options.References.Add(assembly1);        

        // Now we can compile with our custom options, and the compiled code will be able to use public API's defined in the reference assembly
        ScriptAssembly assembly2 = domain.CompileFile(sourcePath2, options);
    }
}
```
