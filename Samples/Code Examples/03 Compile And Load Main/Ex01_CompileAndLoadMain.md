# Compile And Load Main
Roslyn C# supports a concept of a main type defined within an assembly, but unlike the standard C# main (`Program` in the context of a exe application, or where the `Main` entry point is defined for example) Roslyn C# will pick a main type from an assembly based on a few predefined rules, and there are no requirements to specify an entry point.
As a result of this, all `CompileAndLoad...` API's also include an equivilent `CompileAndLoadMain...` API which returns a `ScriptType` object. As an example, `CompileAndLoadSource` would become `CompileAndLoadMainSource` and will return the type that was selected as the main type.

# Main Type Selection
There are a few preset rules that will determined the selected main type, however you can also provide a custom type selector if this beahviour is not sutiable for your use case as covered in the next section.
Using the default behaviour, the type selection rules in order of precedence are:
* Find the first public MonoBehaviour type, or any non-public MonoBehaviour type if no public one was found
* Find the first public class with any base type, or any non-public class if no public one was found
* Find the first public type (Any non-class type: struct, enum, interface, etc.), or any non-public type if no public one was found
* Finally if none of the above criteria was matched, then we simply find the first defined type which is not the built in `<Module>` type, or null if there are no matching types.

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

# Compile And Load Main File
As stated earlier, there are `CompileAndLoadMain...` variants for all available compile requests (source, file, syntax tree, project and folder), but this example will just cover the file API to remain concise.
The usage is much the same as `CompileAndLoad` API's, however there are changes to the method name and return type, which now becomes a `ScriptType` object.

```cs
public class CompileAndLoadMainFile : MonoBehaviour
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
        ScriptType mainType = domain.CompileAndLoadMainFile(sourceFile);

        // Check if compile and load was successful
        // If compilation or code security verification failed, then the mainType will be null and details will be logged in the Unity console by default
        if(mainType != null)
        {
            // Log the name in the console
            // The script type can be used to access members by name
            Debug.Log(mainType.Name);
        }
    }
}
```
