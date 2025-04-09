# Compile From Syntax Tree

This example shows how you can compile one or more `Microsoft.CodeAnalysis.SyntaxTree` using Roslyn C#.
Compiling from a syntax tree means that you can compile source code that is emitted or generated at runtime making the possibilities endless.
Note that this example only covers compiling and will not load the resulting output assembly into the game. For most users it is recommended to check out the CompileAndLoadSyntaxTree example which covers compilation and loading in a single operation.

_Note  - A reference to `Microsoft.CodeAnalysis` and `Microsoft.CodeAnalysis.CSharp` is required in order to build syntax trees. These dependencies are shipped with Roslyn C# and will be auto-referenced by `Assembly-CSharp` and any assembly definitions created by the user - no manualy action is required_

#### Using statements
<details>
  <summary>List of using statements required by code examples provided here</summary>

```cs
using Microsoft.CodeAnalysis;
using Microsoft.CodeAnalysis.CSharp;
using Microsoft.CodeAnalysis.CSharp.Syntax;
using System.Linq;
using UnityEngine;
```
</details>

---

Firstly a single SyntaxTree can be compiled as shown in this example:
```cs
public class CompileSyntaxTree : MonoBehaviour
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

        // Now we can make a compile request for the syntax tree.
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadSyntaxTree' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileSyntaxTree(syntaxTree);

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
```

As in other examples, it is also possible to compile multiple syntax trees together as a batch so that the syntax defined in each tree can reference each other. 
Note that the method name has changed to the plural CompileSyntaxTrees(s)::
```cs
public class CompileSyntaxTree : MonoBehaviour
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

        // Now we can make a compile request for the syntax tree.
        // Note that this compile call will not load the assembly into the game. 
        // See later 'CompileAndLoadSyntaxTree' example to incorporate loading the compiled assembly in a single call.
        CompileResult compileResult = domain.CompileSyntaxTrees(new SyntaxTree[]
        {
          // Note that only 1 syntax tree is added here for this example, but we can provide as many as required
          syntaxTree
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
```
