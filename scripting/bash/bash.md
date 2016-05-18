Bash Scripting
===

- switch between emacs and vi
	- set -o vi
	- set -o emacs

Commands to learn \(read their man pages\)
---
- echo
	- output text
	- `-n`
		suppresses a terminating newline
- printf
	- have to explicitly put newlines in where you want them, but you can use tabs and enhanced number formatting
- read
	- takes input  and puts it into a variable
- mail
- find
- cut
	- separate lines into fields, most commonly used to extract delimited fields
	- can return segments defined by column boundaries as well
	- default delimiter is `<Tab>`, but is changeable via `-d`
	- `-f` specifies which fields  to include in the output
- ps
- grep 
	- searches its input text and prints the lines that match the given pattern
	- `-c`
		- print a count of the matched lines
	- `-v`
		- print nonmatching lines
	- `-l`
		- print only the names of matching files instead of all the lines
	- `-p`
		- uses perl-styled regular expressions but just use perl instead
- uniq
	- kind of like `sort -u` but with a few other options
	- input must be presorted
	- `-c`
		- count the number of instances of each line
	- `-d`
		- show only duplicated lines
	- `-u` 
		- show only unduplicated lines
- sort
	- sorts input lines based on various keys
	- `-b`
		- ignore leading whitespace
	- `-f`
		- case insensitive sorting
	- `-k`
		- specify the columns that form the sort key
	- `-n`
		- compare fields as integer numbers
	- `-r`
		- reverse sort order
	- `-t`
		- set field separator \(default is whitespace\)
	- `-u`
		- output unique records only
- wc
	- counts words, lines, and characters in a file, all three by default
	- `-w`
		- counts the words
	- `-l`
		- counts the lines
	- `-c`
		- counts the characters
- tee
	- copy a file stream so it can be directed to 2 different places
	- takes a file location as an argument to send the copy stream output to
- head and tail
	- head shows the first so many lines in a file
	- tail shows the last so many lines in a file
		- useful for watching lines as they are added with the `-f` option
- fc
	- takes the last command and transfers it to a text editor
- bash 
	- calls a separate instanc of bash to execute something
	- can be used to pipe commands into via echo
		- so we can just echo our commands first to make sure theyre right, and then piped into bash to do them
	- `-x` executes each command before they are executed
- test
	- often used with while or if statements
	- is a separate command, unlike in C language
	- textual operators for numbers, symbolic operators for strings
	- `[]` is a shorthand way of calling test


File associations
---
- `dev/tty` is a synonym for the current terminal
- STDIN=0
- STDOUT=1
- STDERR=2
- /dev/null
	- where to send output if you dont want any
- redirecting
	- `[STDIN,STDOUT,STDERR] **>** <FILE>`
		- write the output of the stream to *file* starting at offset 0
	- `[STDIN,STDOUT,STDERR] **>>** <FILE>`
		- append the output of the stream to the end of *file*
	- `[STDIN,STDOUT,STDERR] **|** <command>` 
		- pipe the output of the stream into *command*
	- `<command> >& <file>`
		- redirect both STDOUT and STDERR to *file*
	- `<command> [1,2]> <file>`
		- redirect STDOUT **or** STDERR to *file*
- setting up environment variables
	- put them in one or both of these files
		- `~/.profile`
		- `~/.bash_profile`
- good places for putting scripts
	- `~/bin`
	- `/usr/local/bin`
- you can put useful functions in
	- `~/.bash_profile`

Using return values
---
- commands return 0 on success, nonzero on failure
- use C language logical operators to conditionally string commands together
	- `<command1> || <command2>`
		- only do *command2* if *command1* failed
	- `<command1> && <command2>`
		- only do *command2* if *command1* succeeded

Variables and Quoting
---
- leave unmarked in assignments, prefix with $ when reading
	- do not put spaces around the variable \(like between variable and =\)
	- use braces `{}` to separate the variable from surrounding text 
		- `echo '${var}th time echo is called'`
- use all cap names for environment variables, and all lowercase with underscores as spaces for local variables
- use **export** to promote local variables to environment variables
	- `export *var*` 
- variables are global within a script
	- subject to shadowing in functions
- all bash variables are strings.  all of them
- arrays
	- delimited by parentheses
	- elements are separated by whitespace
	- quoting can be used to include spaces in an element
	- access individual elements like so
		- `${array_name[subscript]}`
			- always include the curly braces
		- subscripting begins at 0
			- subscript `@` and `*` means all the elements
	- number of elements in the array has 2 options
		- `${#array_name[@]}`
		- `${#array_name[*]}`
	- length of a specific element
		- `${#array_name[subscript]}`
- single vs double quoting
	- double quoted strings get globbed and variables are expanded
	- single quoted strings are not globbed and variables are not expanded
- back-ticks 
	- execute the contents of the string as a shell command and replace the string with the output of the command

Scripting
---
- comments start with a `#`
- more than one command can be on a line by separating the commands with ';'
- use a 'shebang' to tell bash which interpreter to use
	- `#!/bin/bash`
- dont forget to make the script executable or you will have to invoke bash explicitly on the file
	- `chmod +x <file>`
- script development process
	- develop the script as a pipeline, one step at a time, on the command line
	- send the output to standard output and check to be sure it looks right
	- for each step, use the shells command history to recall pipelines and the shells editing features to tweak them
	- once the output is correct, execute the actual commands and verify they worked as intended
	- use `fc` to capture the work and put it into an editor, document it, and save it
- command line arguments
	- referred to by number
		- $0 is the name of the command
		- $1 is the first argument, $2 the second, etc
		- $# is the number of arguments not including $0
		- $* is an array of all the arguments, not including $0
- test the arguments at the beginning of the script to make sure they are appropriate.  If they are not, print a usage statement and return a nonzero return code
- `$(( ... ))`
	- forces numeric evaluation of enclosed expression
	- variables inside dont have to be prefixed with $
	- ++ works as an assignment for the variables inside the expression
	- work in the context of double quotes
- functions
```
function function_name{
	code
	exit values
}
```
	- arguments to functions are treated like arguments to the script
		- $0 is still the command name for the script
	- one person says use functions instead of aliases.  Who knows if he is correct
- if statement
```
if command;then
commands
elif command; then
commands
fi
```
- while statement
```
while command; do
commands
done 
```
- switch \(case statement\)
```
case $var in
	*condition1*) 	command;;  
	*condition2*) 	command;;
	etc
	\*)		command 		#default command
						#last case has no ;;
esac
```
- 2 different for loop options
```
for command in <whitespace separated list>; do
	commands
done
```
```
for (( i=0 ; i < $var ; i++ )); do
	commands
done
```

