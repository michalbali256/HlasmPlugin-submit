# HLASM Language Support

HLASM Language Support is an Visual Studio Code extension that supports the High Level Assembler language. It provides code completion, highlighting and navigation features, shows mistakes in the source, and lets you trace how the conditional assembly is evaluated with a modern debugging experience.

This repository contains source codes for the [HLASM Language Support](https://marketplace.visualstudio.com/items?itemName=broadcomMFD.hlasm-language-support) plugin along with its [documentation](documentation.pdf) and an example workspace that showcases its capabilities. The main repository for the project can be found on [GitHub](https://github.com/eclipse/che-che4z-lsp-for-hlasm).

## Getting Started

To start using the HLASM Language Support extension, **follow these steps**:

1. Install VS Code on your computer.
2. In the VS Code, find the [HLASM Language Support](https://marketplace.visualstudio.com/items?itemName=broadcomMFD.hlasm-language-support) extension and install it.
3. In **File** - **Open Folder...**, select the `example_workspace` folder.
4. There are several example files that show different behavior of HLASM. `tutorial.hlasm` shows the basics.

## Language Features

The HLASM Language Support extension parses and analyzes all parts of a HLASM program. It resolves all ordinary symbols, variable symbols and checks the validity of most instructions. The extension supports conditional and unconditional branching and can define global and local variable symbols. It also expands macros and COPY instructions.

## LSP Features
### Highlighting
The HLASM Language Support extension highlights statements with different colors for labels, instructions, operands, remarks and variables. Statements containing instructions that can have operands are highlighted differently to statements that do not expect operands. Code that is skipped by branching AIF, AGO or conditional assembly is not colored.

![](clients/vscode-hlasmplugin/readme_res/highligting.png)

### Autocomplete
Autocomplete is enabled for the instruction field. While typing, a list of instructions starting with the typed characters displays. Selecting an instruction from the list completes it and inserts the default operands. Variables and sequence symbols are also filled with a value from their scope.

![](clients/vscode-hlasmplugin/readme_res/autocomplete.gif)


### Go To Definition and Find All References
The extension adds the 'go to definition' and 'find all references' functionalities. Use the 'go to definition' functionality to show definitions of variable symbols, ordinary symbols and macros, or open COPY files directly. Use the 'find all references' functionality to show all places where a symbol is used.

![](clients/vscode-hlasmplugin/readme_res/go_to_def.gif)

## Macro Tracer

The macro tracer functionality allows you to track the process of assembling HLASM code. It lets you see step-by-step how macros are expanded and displays values of variable symbols at different points during the assembly process. You can also set breakpoints in problematic sections of your conditional assembly code. 

The macro tracer is not a debugger. It cannot debug running executables, only track the compilation process.

### Configuring the Macro Tracer

1. Open your workspace.
2. In the left sidebar, click the bug icon to open the debugging panel (Ctrl + Shift + D).
3. Click `create a launch.json file`.
   A "select environment" prompt displays.
4. Enter **HLASM Macro tracer**.  
   Your workspace is now configured for macro tracing.

### Using the Macro Tracer

To run the macro tracer, open the file that you want to trace. Then press **F5** to open the debugging panel and start the debugging session.

When the tracer stops at a macro or COPY instruction, you can select **step into** to open the macro or COPY file, or **step over** to skip to the next line.

Breakpoints can be set before or during the debugging session.

![](https://github.com/eclipse/che-che4z-lsp-for-hlasm/raw/master/clients/vscode-hlasmplugin/readme_res/tracer.gif)

## External Macro Libraries and COPY Members
The HLASM Language Support extension looks for locally stored members when a macro or COPY instruction is evaluated. The paths of these members are specified in two configuration files in the .hlasmplugin folder of the currently open workspace. Ensure that you configure these files before using macros from separate files or the COPY instruction.

When you open a HLASM file or manually set the HLASM language for a file, you can choose to automatically create these files for the current program.

The structure of the configuration is based on CA Endevor® SCM. `proc_grps.json` defines processor groups by assigning a group name to a list of directories which are searched in the order they are listed. `pgm_conf.json` provides mapping between source files (open code files) and processor groups. It specifies which list of directories is used with which source file. If a relative source file path is specified, it is relative to the current workspace.

Example `proc_grps.json`:

The following example defines two processor groups, GROUP1 and GROUP2, and a list of directories to search for macros and COPY files.

```
{
  "pgroups": [
    {
      "name": "GROUP1",
      "libs": [
        "ASMMAC/",
        "C:/SYS.ASMMAC"
      ]
    },
    {
      "name": "GROUP2",
      "libs": [
        "G2MAC/",
        "C:/SYS.ASMMAC"
      ]
    }
  ]
}
```

Example `pgm_conf.json`:

The following example specifies that GROUP1 is used when working with `source_code.hlasm` and GROUP2 is used when working with `second_file.hlasm`.

```
{
  "pgms": [
    {
      "program": "source_code",
      "pgroup": "GROUP1"
    },
    {
      "program": "second_file",
      "pgroup": "GROUP2"
    },
  ],
  "alwaysRecognize" : ["*.hlasm", "libs/*.asm"]
}
```
If you have the two configuration files configured as above and invoke the MAC1 macro from `source_code.hlasm`, the folder `ASMMAC/` in the current workspace is searched for a file with the exact name "MAC1". If that search is unsuccessful the folder `C:/SYS.ASMMAC` is searched. If that search is unsuccessful an error displays that the macro does not exist.

There is also the option `alwaysRecognize` which takes an array of wildcards. It allows you to configure two things:
- All files matching these wildcards will always be recognized as HLASM files. 
- If an extension wildcard is defined, all macro and copy files with such extension may be used in the source code. For example, with the extension wildcard `*.hlasm`, a user may add macro `MAC` to his source code even if it is in a file called `Mac.hlasm`.

The program field in `pgm_conf.json` supports wildcards, for example:
```
{
  "pgms": [
    {
      "program": "*",
      "pgroup": "GROUP1"
    }
  ]
}
```
In this example, GROUP1 is used for all open code programs.
