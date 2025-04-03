# Compile From File

This example shows how you can compile one or more strings containing file paths to C# source files (.cs) or any other file type containing C# source text.
Note that this example only covers compiling and will not load the resulting output assembly into the game. For most users it is recommended to check out the CompileAndLoadFile example which covers compilation and loading in a single operation.

Firstly a single file path to a .cs file or other file containins C# text can be compiled as shown in this example:
```cs
public class CompileFile : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Private
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the C# file
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadFile' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileFile(sourceFile);

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

Additionally it is also posisble to compile multiple string paths together in a batch, and that way the code contained within the source files will be able to reference each other as if they were part of the same C# project. Note that the method name has changed to the plural CompileFile(s):
```cs
public class CompileFile : MonoBehaviour
{
    // Private
    private const string sourceFile = "path/to/source/file.cs";

    // Private
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Now we can make a compile request for the C# file
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadFile' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileFiles(new string[]
        {
          // Note that only 1 string is added here for this example, but we can provide as many as required
          sourceFile
        });

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
