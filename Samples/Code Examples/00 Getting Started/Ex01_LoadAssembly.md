# Load Assembly
This example shows how you can load a pre-existing .dll assembly using Roslyn C#. 
Loading via Roslyn C# rather than `Assembly.Load` has the added benefit of optionally performing code security valdiation to check whether the assembly may contain potentially harmful code, 
and then you can also with with the `ScriptAssembly` API which aims to make it easier to find an instantiate types etc.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using System.Linq;
using UnityEngine;
```
</details>

---

#### Load Assembly Path
The following example shows how you can load an assmebly using a file path:
```cs
public class LoadAssembly : MonoBehaviour
{
    // Methods
    private void Start()
    {
        // First create a domain to load the assembly into
        ScriptDomain domain = new ScriptDomain();

        // Security load mode
        // ScriptSecurityMode.EnsureLoad - Do not perform and code validation and just load the assembly
        // ScriptSecurityMode.EnsureSecurity - Perform full code validation and discard the assembly if verification fails
        // ScriptSecurityMode.UseSettings - Use the RoslynC# settings to determine which action to take
        ScriptSecurityMode securityMode = ScriptSecurityMode.UseSettings;

        // Load an assembly - The assembly may be verified using active security restriction depending upon the security mod specified
        // If an assembly fails verification then it will not be loaded and the load method will return null
        ScriptAssembly assembly = domain.LoadAssembly("path/to/assembly.dll", securityMode);

        // Once the assembly is loaded we can use the ScriptAssembly interface which makes some things easier,
        // or it is possible to access the reflection assembly if you want to work with standard reflection API
        Assembly reflectionAssembly = assembly.SystemAssembly;
    }
}
```

#### Load Assembly Image
The following example shows how you can load an assembly from an already loaded image represented as a byte array:
```cs
public class LoadAssembly : MonoBehaviour
{
    // Public
    // For this example we assume that the image was already loaded using File.ReadAllBytes or similar, and contains a valid managed assembly image
    public byte[] assemblyImage;

    // Methods
    private void Start()
    {
        // First create a domain to load the assembly into
        ScriptDomain domain = new ScriptDomain();

        // Security load mode
        // ScriptSecurityMode.EnsureLoad - Do not perform and code validation and just load the assembly
        // ScriptSecurityMode.EnsureSecurity - Perform full code validation and discard the assembly if verification fails
        // ScriptSecurityMode.UseSettings - Use the RoslynC# settings to determine which action to take
        ScriptSecurityMode securityMode = ScriptSecurityMode.UseSettings;

        // Load an assembly - The assembly may be verified using active security restriction depending upon the security mod specified
        // If an assembly fails verification then it will not be loaded and the load method will return null
        ScriptAssembly assembly = domain.LoadAssembly(assemblyImage, securityMode);

        // Once the assembly is loaded we can use the ScriptAssembly interface which makes some things easier,
        // or it is possible to access the reflection assembly if you want to work with standard reflection API
        Assembly reflectionAssembly = assembly.SystemAssembly;
    }
}
```

#### Load Assembly Resources
Finally it is also possible to load an assembly as a text asset from the Unity resources folder. This example assumes that the assembly was saved in the Unity `Resources` folder named `MyAssemblyAsset` by replacing the .dll extension with .txt:

```cs
public class LoadAssembly : MonoBehaviour
{
    // Methods
    private void Start()
    {
        // First create a domain to load the assembly into
        ScriptDomain domain = new ScriptDomain();

        // Security load mode
        // ScriptSecurityMode.EnsureLoad - Do not perform and code validation and just load the assembly
        // ScriptSecurityMode.EnsureSecurity - Perform full code validation and discard the assembly if verification fails
        // ScriptSecurityMode.UseSettings - Use the RoslynC# settings to determine which action to take
        ScriptSecurityMode securityMode = ScriptSecurityMode.UseSettings;

        // Load an assembly - The assembly may be verified using active security restriction depending upon the security mod specified
        // If an assembly fails verification then it will not be loaded and the load method will return null
        ScriptAssembly assembly = domain.LoadAssemblyFromResources("MyAssemblyAsset", securityMode);

        // Once the assembly is loaded we can use the ScriptAssembly interface which makes some things easier,
        // or it is possible to access the reflection assembly if you want to work with standard reflection API
        Assembly reflectionAssembly = assembly.SystemAssembly;
    }
}
```
