# ALX Simple Shell Project

> This is a collaboration project to simulate the command line Shell. This shell looks like the bash and it's named **hsh**

## Authors ##
**Zainab Suleiman**
- [xaynab28@gmail.com]

**Zainab A. Abdulkareem**
- [zabdulkareem01@gmail.com]


## General Requirements For The Project

- All files will be compiled on Ubuntu 20.04 LTS using ```gcc, using the options -Wall -Werror -Wextra -pedantic -std=gnu89```
- All files must end with a new line
- A README.md file, at the root of the folder of the project is mandatory
- Use the Betty style. It will be checked using betty-style.pl and betty-doc.pl
- Shell should not have any memory leaks
- No more than 5 functions per file
- All header files should be ```include guarded```
- A README with the description of the project


## Description of a shell##

A shell is a simple UNIX command language interpreter that reads commands from the standard input and outputs it on the command line.

### How it works
- Shows a prompt while waiting for a command from the enduser
- A child process will be created  which checks the command entered by the user
- Built-ins, aliases in the PATH, and local executable programs will be checked
- Next, the child process is replaced by the command, which accepts arguments
- Once the command is executed, the program accesses the parent process and outputs the prompt to the console.
- Then, the program is ready to receive a new command
- To exit: press Ctrl-D or enter "exit" (with or without a status)
- It also functions in non interactive mode

### How The Code is Compiled:

```gcc -Wall -Werror -Wextra -pedantic -std=gnu89 *.c -o hsh```

### How to use this shell
Once you have compiled all the files with *.c* extension in this repo, run the file
following the format specified below:-

#### **hsh** [filename]

**Note: **hsh** can run interactively and non-interactively. If **hsh** is invoked with standard input not connected to a terminal, it reads and executes received commands in order.

For example:
```
$ echo "echo 'hello'" | ./hsh
'hello'
$
```

If **hsh** is invoked with standard input connected to a terminal (determined by [isatty](https://linux.die.net/man/3/isatty)(3)), an *interactive* shell is opened. When executing interactively, **hsh** displays the prompt `$ ` when it is ready to read a command.

Example:
```
$./hsh
$
```

Alternatively, if command line arguments are supplied upon invocation, **hsh** treats the first argument as a file from which to read commands. The supplied file should contain one command per line. **hsh** runs each of the commands contained in the file in order before exiting.

Example:
```
$ cat hello
echo 'good morning'
$ ./hsh hello
'good morning'
$
```

### Environment

Upon invocation, **hsh** receives and copies the environment of the parent process in which it was executed. This environment is an array of *name-value* strings describing variables in the format *NAME=VALUE*. Some of the environmental variables include:

#### 1) HOME
The home directory of the current user and the default directory. This is equivalent to the built-in command **cd**.

e.g.
```
echo "echo $HOME" | ./hsh
/home/zainab
```

#### 2) PWD
The current working directory.

```
$ echo "echo $PWD" | ./hsh
/home/zainab
```

#### 3) OLDPWD
The past working directory as set by the **cd** command.

```
$ echo "echo $OLDPWD" | ./hsh
/home
```

#### 4) PATH
This is a list of directories in the shell. A null directory name in the path (represented by any of two adjacent colons, an initial colon, or a trailing colon) indicates the current directory.
```
$ echo "echo $PATH" | ./hsh
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

### Execution of The Command

Once a command is executed, **hsh** converts it into a string using `" "` as a delimiter, where the first word is the command and all remaining words are considered arguments to that command. **hsh** then proceeds with the following actions:
1. If the first character of the command is neither a slash (`\`) nor dot (`.`), the shell searches for it in the list of shell builtins. If there exists a builtin by that name, the builtin is invoked.
2. If the first character of the command is none of a slash (`\`), dot (`.`), nor builtin, **hsh** searches each element of the **PATH** environmental variable for a directory containing an executable file by that name.
3. If the first character of the command is a slash (`\`) or dot (`.`) or either of the above searches was successful, the shell executes the named program with any remaining given arguments in a separate execution environment.

### Exit Status

**hsh** returns the exit status of the last command executed, with zero indicating success and non-zero indicating failure.

Once, a command to be executed is missing, a return status of `127`; otherwise, once a command is found but not executable, the return status is `126`. All builtins return zero on success and one or two on incorrect usage (indicated by a corresponding error message).

### Signals 

When a user is using the interactive mode, our shell program ignores `Ctrl+c` input. Conversely, an end-of-file input i.e. `Ctrl+d` exits the program.


For instance: User inputs `Ctrl+d` in the third line.
```
$ ./hsh
$ ^C
$ ^C
$
```

### Variable Replacement

This shell interprets `$` character to mean replacing a variable.

#### a) $ENV_VARIABLE
`ENV_VARIABLE` is substituted with its value.

e.g.:
```
$ echo "echo $PWD" | ./hsh
/home/zainab/simple_shell
```

#### b) $?
`?` is substitued with the return value of the last program executed.

Example:
```
$ echo "echo $?" | ./hsh
0
```

#### c) $$
The second `$` replaces the current ID process.

Example:
```
$ echo "echo $$" | ./hsh
4352
```

### COMMENTING 

This programs automatically ignores all words and characters when `#` character is placed before them.

Example:
```
$ echo "echo 'I am here' #this will be ignored!" | ./hsh
'I am here'
```

### Operators

This shell specially takes into account the following characters and interprets them differently:-

#### 1) ; - Command separator
 `;` between commands allows for their execution sequentially.

Example:
```
$ echo "echo 'hello' ; echo 'world'" | ./hsh
'hello'
'world'
```

#### 2) && - logical 'AND' operator
`command1 && command2`: `command2` is executed if, and only if, `command1` returns an exit status of zero.

Example:
```
$ echo "error! && echo 'hello'" | ./hsh
./hsh: 1: error!: not found
$ echo "echo 'all good' && echo 'hello'" | ./hsh
'all good'
'hello'
```

#### £) || - logical 'OR' operator
`command1 || command2`: `command2` is executed if, and only if, `command1` returns a non-zero exit status.

Example:
```
$ echo "error! || echo 'but still runs'" | ./hsh
./hsh: 1: error!: not found
'but still runs'
```


### hsh SHELL Builtin Commands

#### a) cd
  - Usage: `cd [DIRECTORY]`
  - Changes the current directory of the process to `DIRECTORY`.
  - If no argument is given, the command is interpreted as `cd $HOME`.
  - If the argument `-` is given, the command is interpreted as `cd $OLDPWD` and the pathname of the new working directory is printed to standad output.
  - If the argument, `--` is given, the command is interpreted as `cd $OLDPWD` but the pathname of the new working directory is not printed.
  - The environment variables `PWD` and `OLDPWD` are updated after a change of directory.


#### b) alias
  - Usage: `alias [NAME[='VALUE'] ...]`
  - Handles aliases.
  - `alias`: Prints a list of all aliases, one per line, in the form `NAME='VALUE'`.
  - `alias NAME [NAME2 ...]`: Prints the aliases `NAME`, `NAME2`, etc. one per line, in the form `NAME='VALUE'`.
  - `alias NAME='VALUE' [...]`: Defines an alias for each `NAME` whose `VALUE` is given. If `name` is already an alias, its value is replaced with `VALUE`.


#### c) exit
  * Usage: `exit [STATUS]`
  * Exits the shell.
  * The `STATUS` argument is the integer used to exit the shell.
  * If no argument is given, the command is interpreted as `exit 0`.


#### d) env
  * Usage: `env`
  * Prints the current environment.


#### c) setenv
  * Usage: `setenv [VARIABLE] [VALUE]`
  * Initializes a new environment variable, or modifies an existing one.
  * Upon failure, prints a message to `stderr`.


#### d) unsetenv
  * Usage: `unsetenv [VARIABLE]`
  * Removes an environmental variable.
  * Upon failure, prints a message to `stderr`.
