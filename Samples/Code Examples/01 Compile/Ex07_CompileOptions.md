# Compile Options
So far all compile examples have shown compilation requests using the default (null) options, which will simply use the options defined within the Roslyn C# settings window.
This will usually be fine for most cases, however it is possible to provide completley custom options in a compile request from code.
This applies to all compile API's covered so far, as well as all compile and load API's covered in the next section, all of which accept an options `CompileOptions` parameter which is null by default.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using UnityEngine;
```
</details>

---

# Create Options
Firstly you can create a new options object with default values simply using the constructor
```cs
// All settings will use default values
CompileOptions options = new CompileOptions();
```

# Create Options From Settings
More likely is that you want to use the options specified in the Roslyn C# settings window, and only modify a few options to be different.
This is possible using the `FromSettings` helper method which will load and copy all values defined in the Roslyn C# settings object.
Since a copy is made you are free to modify any value without affecting the main settings object.  

_Note: This is also the default behaviour when `null` is passed for the compile options parameter_

```cs
// Create options with all values initialized to the values set in the Roslyn C# settings window.
CompileOptions options = CompileOptions.FromSettings();
```

# All Options
The following code sample will show all of the available options with a brief explanation, as well as usage.
```cs
public class CompileOptions : MonoBehaviour
{
    // Private
    private const string sourceCode = @"
    using UnityEngine;
    class Example
    {
        static void ExampleMethod()
        {
            Debug.Log(""Hello World"");
        }
    }";

    // Methods
    private void Start()
    {
        // Create the domain that will be used later
        ScriptDomain domain = new ScriptDomain();

        // All Compile... and CompileAndLoad... methods accept an optional CompileOption argument
        // You can use this argument to provide additional compiler options, define symbols, references and more
        // Note that if null is provided for the options argument, then the default behaviour is to use the options specified in the Roslyn C# settings

        // Here we just create a new set of compile options that we can modify how we like
        CompileOptions options = new CompileOptions();

        // In most cases it is probably more likely that you will want to keep all the default options setup in
        // the Roslyn Settings window and only modify only a handful of options. For that we can use:
        // Note that it creates a copy of the settings, so we can modify freely with out affecting the global Roslyn settings
        options = CompileOptions.FromSettings();


        // ### Firstly there are a number of compiler flags that can be changed:
        // OutputDirectory is the path the the compiled assembly will be saved on disk if GenerateInMemory flags is disabled. Default is null
        options.OutputDirectory = "MyDirectory";

        // OutputName is the optional name of the compiled assembly. Default is null which will cause a unique guid name to be used.
        options.OutputName = "MyAssembly";

        // AllowUnsafe determines whether the code is compiled with the unsafe flag set, allowing compiled code to work with pointers and unsafe contexts. Default is false
        options.AllowUnsafe = true;

        // AllowOptimize determines whether the compiler is configured for debug or release modes. Default is false so that debug symbols can be generated for better debugging info
        options.AllowOptimize = true;

        // AllowConcurrentCompile determines whether the compiler service is allowed to take advantage of multiple threads when compiling. Not available on WebGL and default is true
        options.AllowConcurrentCompile = false;

        // Deterministic determines whether the compiler will produce the exact same output assembly for the given source input and configuration. Default is true
        options.Deterministic = false;

        // GenerateInMemory determines whether the compiled assembly will be saved to disk or simple stored in memory. Required on WebGL and recommended on Android, and the default is true
        options.GenerateInMemory = false;

        // GenerateSymbols determines whether the compiled will produce debug symbols along side the output assembly in the format specified by DebugFormat. Default is true
        options.GenerateSymbols = false;

        // WarningLevel simply sets the warning level used by the compiler for reporting purposes. Default is 4
        options.WarningLevel = 3;

        // LanguageVersion is used to specify a specific C# language version such as C# 11 if only a subset of features are required. Default is Default which equates to the latest available version.
        options.LanguageVersion = LanguageVersion.CSharp11;

        // OutputKind is used to specify the type of output produced by the compiler. Default is DynamicLinkLibrary
        options.OutputKind = OutputKind.ConsoleApplication;

        // TargetPlatform is used to specify the platform architecture that the compiled assembly will target. Default is AnyCPU
        options.TargetPlatform = Platform.X64;

        // DebugFormat is the output format of the debug symbols if GenerateSymbols is enabled. Default is PortablePDB
        options.DebugFormat = DebugInformationFormat.Embedded;


        // ### Additionally we can provide other resources to the compiler including define symbols, references, processors, type selectors, and more
        // CompileLogLevel is used to determine which compiler diagnostic messages are logged to the Unity console after compilation. Default is Errors
        options.CompilerLogLevel = LogLevel.Warnings;

        // Next we can add any number of preprocessor define symbols to the options so that we can do conditional compiling
        options.DefineSymbols.Add("UNITY_EDITOR");
        options.DefineSymbols.Add("DEBUG");

        // We can add additional compiler references of type ICompilationReference
        // This topic will be covered in detail in a later example
        //options.References.Add(ICompilationReference)

        // We can add additional parser processors of type IParserProcessor for transforming syntax trees as part of the compilation
        // This topic will be covered in detail in a later example
        //options.ParserProcessors.Add(IParserProcessor)

        // We can add additional compilation processors of type ICompilationProcessor for performing assembly post processing
        // This topic will be covered in detail in a later example
        //options.CompilationProcessors.Add(ICompilationProcessor)

        // We can add an override main type selector of type IMainTypeSelector which is used by Roslyn to select a type from an assembly which is considered to be the main type.
        // Note that this is not the Main type in a traditional C# program context (Program.Main for example), but instead can be any type and is used by Roslyn API's such as `CompileAndLoadMainSource`.
        // The behaviour of the default main type selector is to first look for any MonoBehaviour type preferring public, then any class type preferring public, then any other type that is defined.
        //options.MainTypeSelector = IMainTypeSelector


        // Now we can issue a compile request with our custom options.
        // For the example we will just use CompileSource as covered in a previous example
        domain.CompileSource(sourceCode, options);
    }
}
```
