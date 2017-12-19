---
title: How it Works
layout: singlePage
sectionid: howItWorks
---

A language server runs as a separate process and development tools communicate with the server using the language protocol over JSON-RPC. Below is an example for how a tool and a language server communicate during a routine editing session:

** To Do ** Replace VS Code in the diagram with Development Tool to make it VS Code neutral
![language server protocol](./img/language-server-sequence.png)

* **The user opens a file (referred to as a *document*) in the tool**: The tool notifies the language server that a document is open ('textDocument/didOpen'). From now on, the truth about the contents of the document is no longer on the file system but kept by the tool in memory. The contents now has to be synchronized between the tool and the language server.

* **The user makes edits**: The tool notifies the server about the document change ('textDocument/didChange') and the language representation of the document is updated by the language server. As this happens, the language server analyses this information and notifies the tool with the detected errors and warnings ('textDocument/publishDiagnostics').

* **The user executes "Go to Definition" on a symbol of an open document**: The tool sends a 'textDocument/definition' request with two parameters: (1) the document URI and (2) the text position from where the 'Go to Definition' request was initiated to the server. The server responds with the document URI and the position of the symbol's definition inside the document.

* **The user closes the document (file)**: A 'textDocument/didClose' notification is sent from the tool informing the language server that the document is now no longer in memory. The current contents is now up to date on the file system.

This example illustrates how the protocol communicates with the language server at the level of document references (URIs) and document positions. These data types are programming language neutral and apply to all programming languages. The data types are not from the domain model typically abstract syntax trees and compiler symbols (for example, resolved types, namespaces, ...) from a language implementation. The fact, that the data types are simple and apply to all all languages simplifies the protocol significantly. It is much simpler to standardize a text document URI or a cursor position compared with standardizing an abstract syntax tree and compiler symbols across different programming languages.

Now let's look at the 'textDocument/definition' request in more detail. Below are the payloads that go between the development tool and the language server for the "Go to Definition" request in a C++ document.

This is the request:

```json
{
    "jsonrpc": "2.0",
    "id" : 1,
    "method": "textDocument/definition",
    "params": {
        "textDocument": {
            "uri": "file:///p%3A/mseng/VSCode/Playgrounds/cpp/use.cpp"
        },
        "position": {
            "line": 3,
            "character": 12
        }
    }
}
```

This is the response:

```json
{
    "jsonrpc": "2.0",
    "id": "1",
    "result": {
        "uri": "file:///p%3A/mseng/VSCode/Playgrounds/cpp/provide.cpp",
        "range": {
            "start": {
                "line": 0,
                "character": 4
            },
            "end": {
                "line": 0,
                "character": 11
            }
        }
    }
}
```

When a user is working with different languages, a development tools usually starts a language server for each programming language. The example below shows a session where the user works on Java and SASS files.

![language server protocol](./img/language-server.png)

Not every language server can support all features defined by the protocol. LSP therefore defines  'capabilities'. A capability defines a related group of language features. A development tools and the language server announce their supported feature set. As an example, a server announces that it can handle the 'textDocument/definition' request, but it might not handle the 'workspace/symbol' request. Similarly, development tools can announce that they are able to provide 'about to save' notifications before a document is saved, so that a server can compute textual edits to automatically format the edited document.

**Notice** the actual integration of a language server into a particular tool is not defined by the language server protocol and is left to the tool implementors.

## Libraries (SDKs) for LSP providers and consumers

To simplify the implementation of language servers and clients, there are libraries or SDKs:

- *Development tool SDKs* each development tool typically provides a library for integrating language servers. For example, for JavaScript/TypeScript there is a [language client npm module](https://www.npmjs.com/package/vscode-languageclient)

- *Language Server SDKs* for the different implementation languages there is an SDK to implement a language server in a particular language. For example, to implementat a language server using Node.js there is [language server npm module](https://www.npmjs.com/package/vscode-languageserver).
