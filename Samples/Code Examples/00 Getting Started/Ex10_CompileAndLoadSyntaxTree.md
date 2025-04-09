# Compile And Load From Syntax Tree

This example shows how you can compile and load one or more `Microsoft.CodeAnalysis.SyntaxTree` using Roslyn C#.
Compile and load operations mean that the source will first be compiled, then code security checked if applicable, and then finally loaded into the game so it can be executed when required.

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using RoslynCSharp;
using Trivial.CodeSecurity;
using Microsoft.CodeAnalysis;
using Microsoft.CodeAnalysis.CSharp;
using Microsoft.CodeAnalysis.CSharp.Syntax;
using UnityEngine;
```
</details>

---

#### Compile And Load Syntax Tree
Firstly you can compile and load a source string containg C# code as shown below.

```cs
public class CompileAndLoadSource : MonoBehaviour
{
    // Private
    // Build a syntax tree using Microsoft.CodeAnalysis that we can compile later
    private static readonly CompilationUnitSyntax syntax = SyntaxFactory.CompilationUnit()
        .AddUsings(
            SyntaxFactory.UsingDirective(SyntaxFactory.IdentifierName("UnityEngine")))
        .AddMembers(
            SyntaxFactory.ClassDeclaration("Example")
            .AddMembers(
                SyntaxFactory.MethodDeclaration(SyntaxFactory.PredefinedType(SyntaxFactory.Token(SyntaxKind.VoidKeyword)), "SayHello")
                .AddModifiers(SyntaxFactory.Token(SyntaxKind.PublicKeyword), SyntaxFactory.Token(SyntaxKind.StaticKeyword))
                .AddBodyStatements(
                    SyntaxFactory.ExpressionStatement(
                        SyntaxFactory.InvocationExpression(
                            SyntaxFactory.MemberAccessExpression(SyntaxKind.SimpleMemberAccessExpression,
                            SyntaxFactory.IdentifierName("Debug"),
                            SyntaxFactory.IdentifierName("Log"))
                    .WithOperatorToken(SyntaxFactory.Token(SyntaxKind.DotToken))
                    ).WithArgumentList(
                            SyntaxFactory.ArgumentList(
                                SyntaxFactory.SingletonSeparatedList<ArgumentSyntax>(
                                    SyntaxFactory.Argument(
                                        SyntaxFactory.LiteralExpression(SyntaxKind.StringLiteralExpression,
                                        SyntaxFactory.Literal("Hello World"))))))))));

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Create syntax tree
        SyntaxTree syntaxTree = CSharpSyntaxTree.Create(syntax);

        // Now we can make a compile request for the C# syntax tree
        // Note that this will compile, security check and load the resulting assembly into the game if successful. 
        ScriptAssembly assembly = domain.CompileAndLoadSyntaxTree(syntaxTree);

        // Check if compile and load was successful
        // If compilation or code security verification failed, then the assembly will be null and details will be logged in the Unity console by default
        if(assembly != null)
        {
            // Log the name in the console
            // The script assembly can be used to access types by name, base type and other criteria. See later examples for more info
            Debug.Log(assembly.Name);
        }
    }
}
```

#### Compile And Load Syntax Tree Report
In many cases the above example will work just fine, however there are overloads available that provide detailed information about the compilation result, as well as the code security validation result.
It may be useful when you need to report failures using custom logging, or just would like to know at which stage the operation failed:

```cs
public class CompileAndLoadSource : MonoBehaviour
{
    // Private
    // Build a syntax tree using Microsoft.CodeAnalysis that we can compile later
    private static readonly CompilationUnitSyntax syntax = SyntaxFactory.CompilationUnit()
        .AddUsings(
            SyntaxFactory.UsingDirective(SyntaxFactory.IdentifierName("UnityEngine")))
        .AddMembers(
            SyntaxFactory.ClassDeclaration("Example")
            .AddMembers(
                SyntaxFactory.MethodDeclaration(SyntaxFactory.PredefinedType(SyntaxFactory.Token(SyntaxKind.VoidKeyword)), "SayHello")
                .AddModifiers(SyntaxFactory.Token(SyntaxKind.PublicKeyword), SyntaxFactory.Token(SyntaxKind.StaticKeyword))
                .AddBodyStatements(
                    SyntaxFactory.ExpressionStatement(
                        SyntaxFactory.InvocationExpression(
                            SyntaxFactory.MemberAccessExpression(SyntaxKind.SimpleMemberAccessExpression,
                            SyntaxFactory.IdentifierName("Debug"),
                            SyntaxFactory.IdentifierName("Log"))
                    .WithOperatorToken(SyntaxFactory.Token(SyntaxKind.DotToken))
                    ).WithArgumentList(
                            SyntaxFactory.ArgumentList(
                                SyntaxFactory.SingletonSeparatedList<ArgumentSyntax>(
                                    SyntaxFactory.Argument(
                                        SyntaxFactory.LiteralExpression(SyntaxKind.StringLiteralExpression,
                                        SyntaxFactory.Literal("Hello World"))))))))));

    // Methods
    private void Start()
    {
        // Create the domain as per previous example
        ScriptDomain domain = new ScriptDomain();

        // Create syntax tree
        SyntaxTree syntaxTree = CSharpSyntaxTree.Create(syntax);

        // Now we can make a compile request for the syntax tree
        // Note that this compile, security check and load the resulting assembly into the game if successful.
        CompileResult compileResult;
        CodeSecurityReport securityReport;
        ScriptAssembly assembly = domain.CompileAndLoadSyntaxTree(syntaxTree, out compileResult, out securityReport);

        // Check if compile and load was successful
        // If compilation or code security verification failed, then the assembly will be null and details will be logged in the Unity console by default
        if(assembly != null)
        {
            // Log the name in the console
            // The script assembly can be used to access types by name, base type and other criteria. See later examples for more info
            Debug.Log(assembly.Name);
        }
        else
        {
            // The compilation failed and as in previous exmaples we can get all diagnostic messages from the compile result object
            if(compileResult.Success == false)
            {
                Debug.Log("Compilation Failed!");
            }
            // The code security verification stage has failed (Assembly includes code which is determined to be illegal by the code seuciryt verification rules) and we can get detailed information about the specific from the security report.
            else if(securityReport.IsSecurityVerified == false)
            {
                Debug.Log("Code security verification failed!");
            }
        }
    }
}
```

# Compile Multiple
As in previous examples, there are also plural versions of these methods available for use when you need to compile multiple syntax trees as a batch:
```cs
// Compile and load multiple C# syntax trees 
ScriptAssembly assembly = domain.CompileAndLoadSyntaxTrees(new SyntaxTree[] { treeA, treeB });

// Compile and load multiple C# syntax trees and get detailed information about the result
CompileResult compileResult;
CodeSecurityReport securityReport;
ScriptAssembly assembly = domain.CompileAndLoadSyntaxTrees(new SyntaxTree[] { treeA, treeB }, out compileResult, out securityReport);
```
