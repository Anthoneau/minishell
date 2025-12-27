# minishell

`minishell` is a 42 School system programming project whose goal is to implement a **minimal Unix shell**.

The objective is not to recreate Bash feature-by-feature, but to understand and reimplement the **core mechanisms of a shell**: parsing user input, building commands, handling pipes and redirections, expanding variables, executing processes, and managing signals.

This project is a natural continuation of `pipex` and serves as a concrete introduction to how a shell works internally.

---

## Project Overview

`minishell` reads user input in a loop, interprets it, and executes commands in a way similar to a real shell.

The program supports:
- Command execution via `execve`
- Pipes and redirections
- Environment variable expansion
- Built-in commands
- Signal handling (`Ctrl-C`, `Ctrl-\`, `Ctrl-D`)
- Heredocs

---

## Global Architecture

The project follows a clear execution pipeline:

```
User input
   ↓
Lexer
   ↓
Expander
   ↓
Token checks
   ↓
Parser / AST
   ↓
Execution
```

Each step has a dedicated responsibility and is implemented in its own module.

---

## End-to-End Example

Consider the following command:

```bash
cat << EOF | grep "$USER" | wc -l > result.txt
```

This command combines multiple core shell features:
- heredoc input
- variable expansion
- pipes
- redirections

High-level execution flow:

1. The input is tokenized by the lexer  
   (`cat`, `<<`, `EOF`, `|`, `grep`, `$USER`, `|`, `wc`, `-l`, `>`, `result.txt`)

2. Variables are expanded  
   (`$USER` is replaced by its value)

3. Tokens are validated  
   (correct pipe chaining, valid heredoc and redirection syntax)

4. An AST is created  
   representing a pipeline of three commands with a final output redirection

5. The heredoc reads user input line by line until `EOF`  
   The input is written into a pipe and used as standard input for `cat`

6. Processes are forked and connected:
   - `cat` reads from the heredoc pipe
   - `grep` reads from the first pipe and filters matching lines
   - `wc -l` reads from the second pipe

7. The final output is redirected to `result.txt`

This example illustrates how heredocs, expansion, pipes, and redirections interact within the shell.

---

## Lexer & Tokenization

**Directory:** `lexer/`

The lexer reads the raw input string and splits it into meaningful tokens:
- words
- pipes (`|`)
- redirections (`<`, `>`, `>>`, `<<`)

Quoting rules (`'` and `"`) are handled at this stage to ensure that spaces inside quotes are preserved.

Tokenization is the foundation of the shell: without a correct lexer, nothing else can work reliably.

---

## Token Validation

**Directory:** `check_token/`

Once tokens are created, they are validated to detect syntax errors early.

This step checks for:
- invalid pipe usage
- malformed redirections
- unexpected end of input
- heredoc consistency

Errors are reported before any execution attempt, mimicking the behavior of real shells.

---

## Parser & AST

**Directory:** `parser/`

After validation, tokens are transformed into an **Abstract Syntax Tree (AST)**.

The AST represents:
- command sequences
- pipe relationships
- input/output redirections

Each node in the tree corresponds to a command or operator, allowing the execution phase to traverse the structure logically instead of relying on flat token lists.

---

## Heredoc Handling

**Directory:** `check_token/`

Heredocs (`<<`) are handled before execution.

When a heredoc is encountered, the shell reads user input **line by line until the specified delimiter is reached**, exactly like a real shell.

The heredoc input is written directly into a pipe:
- a child process is spawned to read the user input
- each line is written to the pipe’s write end
- once the delimiter is reached, the pipe is closed

The read end of the pipe is then used as standard input for the corresponding command.

This approach avoids the creation of temporary files and keeps the implementation simple.
Like most shells, this relies on the pipe buffer size; extremely large heredoc inputs may lead to undefined behavior.

---

## Expander

**Directory:** `expander/`

The expander processes tokens and replaces:
- environment variables (`$VAR`)
- special variables (such as `$?`)

Expansion respects quoting rules:
- variables are expanded inside double quotes
- variables are ignored inside single quotes

This step bridges parsing and execution by transforming logical tokens into concrete command arguments.

---

## Execution

**Directory:** `exec/`

The execution module is responsible for running commands.

It handles:
- creation of child processes with `fork`
- command execution via `execve`
- pipe setup between processes
- redirection of file descriptors using `dup2`
- execution of builtins when required

Pipelines are executed by chaining processes and pipes according to the AST structure.

---

## Built-in Commands

**Directory:** `builtins/`

The following builtins are implemented:
- `cd`
- `echo`
- `env`
- `export`
- `unset`
- `pwd`
- `exit`

Builtins are executed in the parent process when necessary (for example `cd`), ensuring correct shell behavior.

Environment variables are stored and managed internally.

---

## Signal Handling

**File:** `signals.c`

Signal handling is essential for user experience.

`minishell` properly manages:
- `Ctrl-C` (`SIGINT`)
- `Ctrl-\` (`SIGQUIT`)
- `Ctrl-D` (EOF)

Signal behavior adapts depending on the shell state:
- prompt
- command execution
- heredoc input

This closely matches the behavior of a real shell.

---

## Project Structure

```text
builtins/       # Built-in shell commands
check_token/    # Syntax checks and heredoc handling
exec/           # Command execution and process management
expander/       # Variable expansion
lexer/          # Tokenization
parser/         # AST creation and redirections
utils/          # Helper functions
main.c          # Program entry point
signals.c       # Signal handling
```

The codebase is organized to keep each concept isolated and readable.

---

## Why This Project Matters

Although complex, `minishell` is one of the most formative projects of the 42 curriculum.

It helped me understand:
- how a shell interprets user input
- how processes and pipes interact
- how execution context changes between parent and child
- why parsing and validation are critical before execution

---

## Usage

### Requirements

`minishell` relies on the `readline` library.

#### macOS
- Homebrew required
- Install readline:
```bash
brew install readline
```

#### Linux
- Install readline development files:
```bash
sudo apt-get install libreadline-dev
```
- `pkg-config` is required for compilation

---

### Build & Run

```bash
make
./minishell
```

---

## Author

**Anthony Goldberg** ***agoldber***

42 student – core curriculum completed