# Compile From Folder
This example shows how you can find and compile all c# (.cs) files in a specific directory, with the option to search recursivley or top level only. 
Note that this example only covers compiling and will not load the resulting output assembly into the game. For most users it is recommended to check out the CompileAndLoadFolder example which covers compilation and loading in a single operation.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using System.IO;
using System.Linq;
using UnityEngine;
```
</details>

---

#### Compile Folder
The below example shows how you can compile all files within a given folder path:
```cs
public class CompileFolder : MonoBehaviour
{
    // Private
    private const string folderPath = "path/to/folder";

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Search option must be provided when compiling a folder.
        // It is used to determine whether source files in sub-folders should also be included
        SearchOption searchOption = SearchOption.AllDirectories;

        // Now we can make a compile request for all C# files in the folder.
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadFolder' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileDirectory(folderPath, searchOption);

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
