# Compile And Load Async

All of the compile and load methods covered so far also have equivilent async variants by appending Async to the method name. For example: `CompileAndLoadSource` will become `CompileAndLoadSourceAsync` and all compile async variants return a `CompileAsync<ScriptAssembly>` object. This example shows how you can use the async variations either in a coroutine or in a C# async/await context.

Only `CompileAndLoadFileAsync` method will be covered here to keep this example concise, however the exact same principals apply to all CompileAndLoad...Async variants

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

#### Compile And Load Async Coroutine
Firstly you can wait for an async compile and load request to complete in coroutine in a similar way that you would use many Unity async API's
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
        CompileAsync<ScriptAssembly> async = domain.CompileAndLoadFileAsync(sourceFile);

        // Wait for the compile to complete as you would with any other Unity async API call.
        yield return async;

        // Now we can check if the compile and load request was successful
        if(async.IsSuccessful == true)
        {
            // Once finished we can get the loaded assembly object.
            ScriptAssembly result = async.Result;
        }
        else
        {
            // Log the error in the console
            Debug.LogError(async.Status);
        }
    }
}
```

#### Compile And Load Async Await
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
        CompileAsync<ScriptAssembly> async = domain.CompileAndLoadFileAsync(sourceFile);

        // Wait for completed
        await async.Task;

        // Now we can check if the compile and load request was successful
        if(async.IsSuccessful == true)
        {
            // Once finished we can get the loaded assembly object.
            ScriptAssembly result = async.Result;
        }
        else
        {
            // Log the error in the console
            Debug.LogError(async.Status);
        }
    }
}
```
