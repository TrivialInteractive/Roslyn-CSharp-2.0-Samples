# Compile Async
All of the compile methods coverd so far also have equivilent async variants by appending `Async` to the method name. 
For example: `CompileSource` will become `CompileSourceAsync` and all compile async variants return a CompileAsync object.
This example shows how you can use the async variations either in a coroutine or in a C# async/await context.

Only `CompileFileAsync` method will be covered here to keep this example concise, however the exact same principals apply to all `Compile...Async` variants (But not `CompileAndLoad...Async variants - see later CompileAndLoadAsync example)

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using System.Collections;
using System.Linq;
using UnityEngine;
```
</details>

---

#### Compile Async Coroutine
Firstly you can wait for an async compile request to complete in coroutine in a similar way that you would use many Unity async API's
```cs
public class CompileFileAsync : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Methods
    private IEnumerator Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can call an async compile method, all of which return a CompileAsync object
        CompileAsync async = domain.CompileFileAsync(sourceFile);

        // Wait for the compile to complete as you would with any other Unity async API call.
        yield return async;

        // Once finished we can get the compile result object and check if it was successful as per previous examples.
        CompileResult result = async.CompileResult;
    }
}
```

#### Compile Async Await
In many cases it may be preferable or more fitting with your game architecture to use the C# async await feature to continue once the compilation has completed.
The CompileAsync object exposes a `.Task` property for this purpose in a simlar way to Unity addressables which can be used to await the call:
```cs
public class CompileFileAsync : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Methods
    private async void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can call an async compile method, all of which return a CompileAsync object
        CompileAsync async = domain.CompileFileAsync(sourceFile);

        // Wait for completed
        await async.Task;

        // Once finished we can get the compile result object and check if it was successful as per previous examples.
        CompileResult result = async.CompileResult;
    }
}
```
