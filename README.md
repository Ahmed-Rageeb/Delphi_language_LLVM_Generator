

# Delphi to WebAssembly Compiler

This project implements a compiler that translates Delphi code into LLVM Intermediate Representation (IR), with an optional extension to compile the IR to WebAssembly (WASM) for execution in web browsers.

## Project Overview

This compiler builds upon previous work (Projects 1 & 2) by reusing the lexer, parser, and AST infrastructure. Instead of interpreting the Pascal/Delphi code directly, this version generates LLVM IR which can be further compiled to executable formats including WebAssembly.

### Features Implemented 

The compiler supports the following Pascal/Delphi language features:

- Classes and Objects
- Constructors and Destructors
- Encapsulation 
- while-do and for-do loops
- break and continue keywords
- user-defined procedures and functions

## Prerequisites

- IntelliJ IDEA (or any Java-supported IDE)
- Java Development Kit (JDK) 11 or newer
- ANTLR4 (must be installed and configured)
- ANTLR4 Plugin for your IDE (optional but recommended)
- LLVM toolchain (version 18.1.1)
- For WebAssembly compilation (optional):
  - Node.js (for testing the web interface)

## Project Structure

```
delphi-compiler/
├── src/
│   ├── main/
│   │   ├── antlr4/
│   │   │   └── pascal.g4             # Delphi grammar definition
│   │   ├── java/
│   │   │   ├── compiler/
│   │   │   │   ├── delphiBaseListener.java  # Base listener for parser
│   │   │   │   ├── LLVMCodeGenerator.java   # LLVM IR code generator
│   │   │   │   ├── delphiBaseVisitor.java   # Base visitor implementation
│   │   │   │   ├── delphiLexer.java         # Generated lexer
│   │   │   │   ├── delphiListener.java      # Generated listener interface
│   │   │   │   ├── delphiParser.java        # Generated parser
│   │   │   │   └── delphiVisitor.java       # Generated visitor interface
│   │   │   └── Main.java                    
│   ├── test/
│   │   ├── pascal/
│   │   │   └── *.pas                        # Test Pascal/Delphi programs
│   │   └── expected/
│   │       └── *.ll                         # Expected LLVM IR output
├── out/
│   └── *.ll                                 # Generated LLVM IR files
├── wasm/                                    # WebAssembly related files (for EC)
│   ├── *.wasm                               # Compiled WebAssembly modules
│   ├── index.html                           # HTML container for WASM execution
│   └── wasm_loader.js                       # JavaScript WASM interface
├── build.gradle                             # Gradle build configuration
└── README.md                                # This file
```

## Building the Compiler

### Using IntelliJ IDEA

1. Open the project in IntelliJ IDEA
2. Make sure ANTLR4 is properly configured in your project
3. Generate ANTLR code from the grammar file:
   - Right-click on `delphi.g4`
   - Select "Generate ANTLR Recognizer"
   - The generated files (delphiLexer.java, delphiParser.java, delphiListener.java, delphiVisitor.java, etc.) will be placed in the appropriate package
4. Build the project (Build > Build Project)

## Using the Compiler

### Generating LLVM IR

To run the generated LLVM IR:

```bash
./gradlew run --args="lli output.ll"
```

### Setting Up The Source File

1. Open LLVMCodeGenerator.java in IntelliJ
2. Locate line 2129 "String pasFilePath = "filepath/[testfile] e.g. test4.pas"."
3. Replace with the path to your test file
4. Corresponding .ll files are also on the folder

### Viewing Generated LLVM IR

The compiler will generate LLVM IR in text format (.ll files). You can examine these files with any text editor to see the generated code.

## Extra Credit: Compiling to WebAssembly and Running in Browser

### Compiling LLVM IR to WebAssembly

Once you have generated the LLVM IR, you can compile it to WebAssembly using LLVM 18.1.1:

```bash
# Using llc to compile to WASM object file
llc-18 -march=wasm32 -filetype=obj output.ll -o output.o

# Link with WASM
wasm-ld --no-entry --allow-undefined --export-all -o output.wasm output.o
```

Note: Commands may vary depending on your LLVM installation. The above commands assume LLVM 18 binaries are available as `llc-18`. Adjust as needed for your system configuration.

### Running WebAssembly in the Browser

1. Open the `index.html` file in your browser
2. The JavaScript in this file will:
   - Fetch the WebAssembly module
   - Instantiate it
   - Call the exported functions
   - Display the results

Alternatively, you can start a local web server using XAMPP and visit `http://localhost:8000` in your browser.

## Compiler Implementation Details

### Code Generation Process

1. **Parsing**: The Delphi code is parsed using ANTLR4, generating a parse tree.
2. **AST Construction**: A custom Abstract Syntax Tree is built from the parse tree.
3. **LLVM IR Generation**: The AST is traversed to generate corresponding LLVM IR instructions.
4. **Output**: The LLVM IR is written to an output file.

### LLVM IR Generation

The LLVM IR generator traverses the AST and produces LLVM IR instructions for each node type:

- Variables are allocated on the stack using `alloca` instructions
- Arithmetic operations use the appropriate LLVM instructions (`add`, `sub`, `mul`, etc.)
- Control flow is implemented using LLVM's branching and labels
- Function calls are translated to LLVM function calls
- I/O operations are implemented using external function calls



### WebAssembly Generation (Extra Credit)

For the WebAssembly compilation, the project:

1. Uses LLVM's `llc` tool to compile the IR to WebAssembly object files
2. Links the object files with wasm-ld to create a final `.wasm` file
3. Provides JavaScript glue code to load and execute the WebAssembly module

## Running the Test Suite

To verify the compiler works correctly, you can run the test suite:

```bash
./gradlew test
```

This will:
1. Compile each test case to LLVM IR
2. Compare the generated IR against expected output
3. For WebAssembly tests, run the WASM module and verify the output



## Resources

--Intellij +Antrl4 
  Source: https://www.youtube.com/watch?v=svEZtRjVBTY
## Video Demonstration

A video demonstration of this project can be found at:

https://ufl.zoom.us/rec/play/SRxdPitLvRUebQQspGDd8c6-vqq5pY-SkdGKTIhtGrMB3799-kY8lYvhf8FlcjWHO6Q4LnC3mNvZA7Cx.zALxpcTSZtBZDnuE?accessLevel=meeting&canPlayFromShare=true&from=share_recording_detail&continueMode=true&componentName=rec-play&originRequestUrl=https%3A%2F%2Fufl.zoom.us%2Frec%2Fshare%2FTHayDKw1-qIklFH_zoLgbfJal6_Jpb5wGS6ERT6Nc7JyIbWqUo-p3nEYJ3GhT9pG.ssaxeJyRg7NAivW8&autoplay=true&startTime=1746207485000
