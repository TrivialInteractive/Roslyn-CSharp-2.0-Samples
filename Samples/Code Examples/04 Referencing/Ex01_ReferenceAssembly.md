# Reference Assembly
Roslyn C# now uses assembly reference assets exclusivley for referencing other assemblies from compiled code, since referencing by file path can cause issues on some platforms.
It is however still possible to reference an assembly by path if required from code, but is not recommended in most cases.

_For adding references to runtime, Unity or other modules/assemblies, see the user guide for creating and adding assembly reference assets via the Roslyn C# settings for support on all platforms_

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using UnityEngine;
```
</details>

---

# Reference Assembly Path
You can create a referenceable object from an assembly path as shown below. It is suitable for use in editor and desktop platforms, but note that the path may change between an editor and standalone build making the reference become invalid:
```cs
public class ReferenceAssembly : MonoBehaviour
{
    // Private
    private const string sourcePath = "path/to/source.cs";
    private const string assemblyPath = "path/to/assembly.dll";

    // Methods
    private void Start()
    {
        // Create the assembly source
        // Assembly source implements `ICompilationReference` so can be passed as a reference in compile requests
        AssemblySource referenceAssembly = AssemblySource.FromFile(assemblyPath);

        // Create compile options with additional references
        // We can use `FromSettings` here because we want to keep all options setup in the Roslyn C# settings, but need to add additional references
        CompileOptions options = CompileOptions.FromSettings();

        // Add our extra reference
        // We can add as many as we like, but for this example one extra reference is added
        options.References.Add(referenceAssembly);

        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can compile with our custom options, and the compiled code will be able to use public API's defined in the reference assembly
        CompileResult compileResult = domain.CompileFile(sourcePath, options);
    }
}
```
