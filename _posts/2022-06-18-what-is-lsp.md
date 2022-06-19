---
layout: post
title: What is LSP and why it's important
subtitle: 
tags: [development, dotnet, dotnet-community]
comments: true
---
Because of [all](https://github.com/omnisharp/omnisharp-vscode/issues/5276) [events](https://isdotnetopen.com/) in .Net Community around LSP and open source, I decided to write an article about LSP and why it is important.

Language Server Protocol allows development tools to communicate with code analyzers in a standardized way. It helps to reuse code base through different development tools that support LSP: Visual Studio Code, Atom, Emacs, etc. Language Server should implement the Language Server Protocol to make this work.

## How Language Server Works
Language Server lives in a separate process, and the development tool can communicate with it via RPC. For example, when a user executes the command 'Go to definition,' IDE sends the command to the language server and receives the document location in return. Language Server uses Roslyn API to perform code analysis in C# case. Development tool can have several open sessions with different language servers for different file types. With Visual Studio Code, you need to write two parts - language server in any language and language client as Visual Studio Code extension in TypeScript/JavaScript.

## Hands-on Language Server 
I want to try to write my language server with VS Code extension as client in the next post. 

## What to read
- https://microsoft.github.io/language-server-protocol/
- https://docs.microsoft.com/en-us/visualstudio/extensibility/language-server-protocol
- https://code.visualstudio.com/api/language-extensions/language-server-extension-guide
- https://martinbjorkstrom.com/posts/2018-11-29-creating-a-language-server