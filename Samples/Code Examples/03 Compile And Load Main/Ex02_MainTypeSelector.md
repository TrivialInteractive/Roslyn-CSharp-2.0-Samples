# Main Type Selector

Roslyn C# uses a main type selector when loading compiled code as an assembly, and it's purpose is to determine which type defined within that assembly is considered to be the main type. 

There are a few preset rules that will determined the selected main type, however you can also provide a custom type selector if this beahviour is not sutiable for your use case as covered in the next section. Using the default behaviour, the type selection rules in order of precedence are:

* Find the first public MonoBehaviour type, or any non-public MonoBehaviour type if no public one was found
* Find the first public class with any base type, or any non-public class if no public one was found
* Find the first public type (Any non-class type: struct, enum, interface, etc.), or any non-public type if no public one was found
* Finally if none of the above criteria was matched, then we simply find the first defined type which is not the built in <Module> type, or null if there are no matching types.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using UnityEngine;
```
</details>

---

# Create Custom Type Selector

If the default main type selection rules are not suitable for your project, then it is possible to implement a custom type selector by implementing the `IMainTypeSelector` interface. 
In this type you can define your own logic for selecting the main type from all types defined in the assembly, and it can then be used later via `CompileAndLoad...` or `CompileAndLoadMain...` API's to return the main type.

```cs
public class CustomTypeSelector : IMainTypeSelector
{
    // Methods
    // The interface only has 1 method to implement, and should return the main type from the `allTypes` collection
    public ScriptType SelectMainType(ScriptAssembly definingAssembly, IReadOnlyList<ScriptType> allTypes)
    {
        // Add type selection logic here. 
        // For this example we just return the first type defined
        return allTypes[0];
    }
}
```

# Use Custom Type Selector

The following examples show how you can use a custom main type selector in Roslyn C#

### Load Assembly

Firstly if you are loading an assembly rather than compiling then you can simply pass in an instance of the custom type selector as an argument.

```cs
public class MainTypeSelectorExample : MonoBehaviour
{
    // Private
    private const string assemblyFile = "path/to/assembly.dll";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // When loading an assembly, we can now pass out custom type selector as the `IMainTypeSelector` parameter
        // This will then call the custom type selector when `ScriptAssembly.MainType` is used 
        ScriptAssembly assembly = domain.LoadAssembly(assemblyFile, ScriptSecurityMode.UseSettings, new CustomTypeSelector());
    }
}
```

### Compile code

For `CompileAndLoad...` or `CompileAndLoadMain...` API's, you can provide a custom type selector via the `CompileOptions` parameter.

```cs
public class MainTypeSelectorExample : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Create options from settings
        CompileOptions options = CompileOptions.FromSettings();

        // Setup the main type selector
        options.MainTypeSelector = new CustomTypeSelector();

        // Now we can pass the compile options with the custom type selector
        // This will then call the custom type selector when `ScriptAssembly.MainType` is used 
        ScriptAssembly assembly = domain.CompileAndLoadSource(sourceFile, options);
    }
}
```
