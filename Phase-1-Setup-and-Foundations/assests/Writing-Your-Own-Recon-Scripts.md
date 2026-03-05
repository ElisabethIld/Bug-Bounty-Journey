The first line of every shell script you write should be *the shebang line*.

It starts with `#!`

### Scan only `scanme.nmap.org`:
```
#!/bin/bash
nmap scanme.nmap.org
/PATH/TO/dirsearch.py -u scanme.nmap.org -e php
```

- `$1` the first argument passed in,
- `$2` is the second argument, and so on.
- `$@` represents all arguments passed in, while
- `$#` represents the total number of arguments.

### Scan for `whatever domain`:
```
#!/bin/bash
nmap $1
/PATH/TO/dirsearch.py -u $1 -e php
```

`PATH` variable, an environmental variable in *Unix systems* that specifies where executable binaries are found.

If you run this command to add `Dirsearch’s` directory to your `PATH`, you can run the tool *from anywhere* without needing to specify its absolute path:
```
export PATH="PATH_TO_DIRSEARCH:$PATH"
```

After executing this command, you should be able to use `Dirsearch` directly:
```
#!/bin/bash
nmap $1
dirsearch.py -u $1 -e php
```

Will have to run the `export` command again after you *restart* your terminal for your `PATH` to contain the path to `Dirsearch`. 

You can add the `export` command to your `~/.bash_profile` file, a file that stores *your bash preferences* and *configuration*. 

Opening `~/.bash_profile` with text editor and adding the `export` command *to the bottom* of the file. 

Save the script in your current directory with the filename `recon.sh`

- `.sh` extension for shell scripts.

Execute the script in the terminal: `$ ./recon.sh`

Adding executing rights for everyone by running in the terminal: `$ chmod +x recon.sh`

Or  for the owner of the script only: `$ chmod 700 recon.sh`

### Run the script, try passing in `scanme.nmap.org` as the first argument:
```
$ ./recon.sh scanme.nmap.org
```

## Saving Tool Output to a File

- *Input redirection* is using the content of a file, or the output of another program, as the input to your script.
- *Output redirection* is redirecting the output of a program to another location, such as to a file or another program.

### The most useful redirection operators:

- `PROGRAM > FILENAME` writes the program’s output *into the file* with that name (it *will clear* any content from the file first, also *create* the file if the file does not already exist);

- `PROGRAM >> FILENAME` appends the output of the program *to the end* of the file, *without clearing* the file’s original content;

- `PROGRAM < FILENAME` *reads* from the file and uses its content as the program input;

- `PROGRAM1 | PROGRAM2` uses the *output* of `PROGRAM1` as the *input* to `PROGRAM2`.

### Write the results of the `nmap` and `dirsearch` scans into different files:
```
#!/bin/bash
echo "Creating directory $1_recon."
mkdir $1_recon
nmap $1 > $1_recon/nmap
echo "The results of nmap scan are stored in $1_recon/nmap."
/PATH/TO/dirsearch.py -u $1 -e php --simple-report=$1_recon/dirsearch
echo "The results of dirsearch scan are stored in $1_recon/dirsearch."
```
- `echo` prints a message to the terminal.
- `mkdir` creates a directory with the name `DOMAIN_recon`
- store the results of `nmap` into a *file* named `nmap` in the newly created directory.
- dirsearch’s `simple-report` flag generates a report in the designated location. Store the results of `dirsearch` to a file named *dirsearch* in the new directory.

### Variables in *bash* can be assigned using the syntax (no spaces around the `=` sign):
```
VARIABLE_NAME=VARIABLE_VALUE
```
### The syntax for referencing variables:
```
$VARIABLE_NAME
```

```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
DOMAIN=$1
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
nmap $DOMAIN > $DIRECTORY/nmap
echo "The results of nmap scan are stored in $DIRECTORY/nmap."
$PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php –simple-report=$DIRECTORY/dirsearch
echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
```
- use `${DOMAIN}_recon` instead of `$DOMAIN_recon` bash would recognize the entirety of `DOMAIN_recon` as the variable name (the `{}` tell).
- we also stored the `$PATH_TO_DIRSEARCH` in *a variable* (to make it easy to change in the future).

## Adding the `Date` of the Scan to the Output:

*Command substitution*, or operating on the output of a command. 

- `$()` tells Unix to execute the command surrounded by the parentheses and assign its output to the value of a variable.
```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
TODAY=$(date)
echo "This scan was created on $TODAY"
DOMAIN=$1
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
nmap $DOMAIN > $DIRECTORY/nmap
echo "The results of nmap scan are stored in $DIRECTORY/nmap."
$PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
```
- `TODAY=$(date)` assign the output of the `date` command to the variable `TODAY`
- `echo "This scan was created on $TODAY"` this lets us output a message indicating the *day* on which we performed the scan.

## Adding `Options` to Choose the Tools to Run:
### Conditionals in bash, the syntax of an `if` statement, the conditional statement ends with the `fi` keyword:
```
if [condition 1]
then
 # Do if condition 1 is satisfied
elif [condition 2]
then
 # Do if condition 2 is satisfied, and condition 1 is not satisfied
else
 # Do something else if neither condition is satisfied
fi
```

To be able to specify the scan `MODE`: `$ ./recon.sh scanmme.nmap.org MODE`.

```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
TODAY=$(date)
echo "This scan was created on $TODAY"
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
if [ $2 == "nmap-only" ]
then
 nmap $DOMAIN > $DIRECTORY/nmap
 echo "The results of nmap scan are stored in $DIRECTORY/nmap."
elif [ $2 == "dirsearch-only" ]
then
 $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php –simple-report=$DIRECTORY/dirsearch
 echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
else
 nmap $DOMAIN > $DIRECTORY/nmap
 echo "The results of nmap scan are stored in $DIRECTORY/nmap."
 $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
 echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
fi
```

- `"nmap-only"` run `nmap` only and store the results to a file named *nmap*.
- `"dirsearch-only"` execute and store the results of `dirsearch` only.
- if the user specifies neither - `else` - run both scans - `nmap $DOMAIN > $DIRECTORY/nmap`.

### Can make your tool run only the `nmap` or `dirsearch` commands by specifying one of these:
```
$ ./recon.sh scanme.nmap.org nmap-only
$ ./recon.sh scanme.nmap.org dirsearch-only
```

## Running Additional Tools
### For example, you want to switch between these three modes or run all three recon tools at once:
```
$ ./recon.sh scanme.nmap.org nmap-only
$ ./recon.sh scanme.nmap.org dirsearch-only
$ ./recon.sh scanme.nmap.org crt-only
```

The syntax of `case` statements (without a long list of `if-else`). Note that the statement ends with `esac` (`case` backward):
```
case $VARIABLE_NAME in
 case1)
  Do something
  ;;
 case2)
  Do something
  ;;
 caseN)
  Do something
  ;;
 *)
  Default case, this case is executed if no other case matches.
  ;;
esac
```

### Improve our script with `case` (instead of multiple `if-else`):
```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
TODAY=$(date)
echo "This scan was created on $TODAY"
DOMAIN=$1
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
case $2 in
 nmap-only)
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
  ;;
 dirsearch-only)
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
  ;;
 crt-only)
  curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
  echo "The results of cert parsing is stored in $DIRECTORY/crt."
  ;;
 *)
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
  curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
  echo "The results of cert parsing is stored in $DIRECTORY/crt."
  ;;
esac
```
- the `curl` command downloads the content of a page.
- `-o` option lets you specify an output file.

### The syntax of a bash `function`:
```
FUNCTION_NAME()
{
 DO_SOMETHING
}
```

Let’s add `functions` to the script:
```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
TODAY=$(date)
echo "This scan was created on $TODAY"
DOMAIN=$1
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
nmap_scan()
{
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
}
dirsearch_scan()
{
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
}
crt_scan()
{
  curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
  echo "The results of cert parsing is stored in $DIRECTORY/crt."
}
case $2 in
 nmap-only)
  nmap_scan
  ;;
 dirsearch-only)
  dirsearch_scan
  ;;
 crt-only)
  crt_scan
  ;;
 *)
  nmap_scan
  dirsearch_scan
  crt_scan
  ;;
esac
```

We created three functions:
- `nmap_scan`,
- `dirsearch_scan`, and
- `crt_scan`

We put the `scan` and `echo` commands in these functions so we can call them repeatedly without writing the same code over and over.

All bash variables are *GLOBAL except* for input parameters like `$1`, `$2`, and `$3`.

Values like `$1`, `$2`, and `$3` can refer only to the values the function is called with, so you can’t use a script’s input arguments within a function, like this:
```
nmap_scan()
{
  nmap $1 > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
}
nmap_scan
```
Here:
- the `$1` in the function refers to the first argument that `nmap_scan` was called with, not the argument our `recon.sh` script was called with. Since
`nmap_scan` wasn’t called with any arguments, `$1` is blank.

## Parsing the Results

*Global Regular Expression Print (`grep`)* to search for a certain piece of information in the output files.
```
grep password file.txt
```
- to search for the string `password`
- in the file `file.txt`

Search the `nmap` output file to see if the target has `port 80` open:
```
$ grep 80 TARGET_DIRECTORY/nmap
80/tcp open http
```

###  A *regular expression*, or `regex`
Is a special string that describes a search pattern.

For example, the output of the `nmap` command looks like this:
```
Starting Nmap 7.60 ( https://nmap.org )
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.065s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 992 closed ports
PORT STATE SERVICE
22/tcp open ssh
25/tcp filtered smtp
80/tcp open http
135/tcp filtered msrpc
139/tcp filtered netbios-ssn
445/tcp filtered microsoft-ds
9929/tcp open nping-echo
31337/tcp open Elite
Nmap done: 1 IP address (1 host up) scanned in 2.43 seconds
```

You might want *to trim* the irrelevant messages from the file so it looks more like this:
```
PORT STATE SERVICE
22/tcp open ssh
25/tcp filtered smtp
80/tcp open http
135/tcp filtered msrpc
139/tcp filtered netbios-ssn
445/tcp filtered microsoft-ds
9929/tcp open nping-echo
31337/tcp open Elite
```
↑
Command *to filter out* the messages at the **start** and **end** of `nmap`’s output and keep only the essential part of the report:
```
grep -E "^\S+\s+\S+\s+\S+$" DIRECTORY/nmap > DIRECTORY/nmap_cleaned
```
- `-E` flag tells `grep` you’re using a regex,
- `\s+` would match any whitespace one or more characters long,
- `\S+` would match any non-whitespace one or more characters long,
- `"^\S+\s+\S+\s+\S+$"` specifies that we should extract lines that contain three strings separated by two whitespaces.

To account for *extra whitespaces* that might be in the command output, let’s *add two more* optional spaces around our search string:
```
"^\s*\S+\s+\S+\s+\S+\s*$"
```

A `regex` consists of two parts:
- *Constants* are sets of strings, while
- *Operators* are symbols that denote operations over these strings.

### `Regex` operators that represent *characters*:
- `\d` matches any digit,
- `\w` matches any character,
- `\s` matches any whitespace, and
- `\S` matches any non-whitespace,
- `.` matches with any single character,
- `\` escapes a special character,
- `^` matches the start of the string or line,
- `$` matches the end of the string or line.

Specify the *number* of characters to match:
- `*` matches the preceding character zero or more times,
- `+` matches the preceding character one or more times,
- `{3}` matches the preceding character three times,
- `{1, 3}` matches the preceding character one to three times,
- `{1, }` matches the preceding character one or more times,
- `[abc]` matches one of the characters within the brackets,
- `[a-z]` matches one of the characters within the range of *a* to *z*,
- `(a|b|c)` matches either *a* or *b* or *c*.

#### Regex syntax - RexEgg’s cheat sheet - https://www.rexegg.com/regex-quickstart.html

## Building a Master Report

- `jq` a command line utility that processes `JSON`.

If we examine the `JSON` output file from `crt.sh`, we need to extract the `name_value` field of each certificate item to extract domain names:
```
$ jq -r ".[] | .name_value" $DOMAIN/crt
```
- `-r` flag tells `jq` to write the output directly to standard output rather than format it as `JSON` strings,
- `.[]` iterates through the array within the `JSON` file, and
- `.name_value` extracts the `name_value` field of each item,
- `$DOMAIN/crt` is the input file to the `jq` command.

#### How `jq` works - https://stedolan.github.io/jq/manual/

To combine all output files into a master report:
```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
DOMAIN=$1
DIRECTORY=${DOMAIN}_recon
echo "Creating directory $DIRECTORY."
mkdir $DIRECTORY
nmap_scan()
{
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
}
dirsearch_scan()
{
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
}
crt_scan()
{
  curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
  echo "The results of cert parsing is stored in $DIRECTORY/crt."
}
case $2 in
 nmap-only)
  nmap_scan
  ;;
 dirsearch-only)
  dirsearch_scan
  ;;
 crt-only)
  crt_scan
  ;;
 *)
  nmap_scan
  dirsearch_scan
  crt_scan
  ;;
esac
echo "Generating recon report from output files..."
TODAY=$(date)
echo "This scan was created on $TODAY" > $DIRECTORY/report
echo "Results for Nmap:" >> $DIRECTORY/report
grep -E "^\s*\S+\s+\S+\s+\S+\s*$" $DIRECTORY/nmap >> $DIRECTORY/report
echo "Results for Dirsearch:" >> $DIRECTORY/report
cat $DIRECTORY/dirsearch >> $DIRECTORY/report
echo "Results for crt.sh:" >> $DIRECTORY/report
jq -r ".[] | .name_value" $DIRECTORY/crt >> $DIRECTORY/report
```

- `$DIRECTORY/report` create a new file named `report` and write today’s date into it,
- `$DIRECTORY/nmap >> $DIRECTORY/report` append the results of the `nmap` and `dirsearch` commands into the `report` file,
- `cat $DIRECTORY/dirsearch >> $DIRECTORY/report`. The `cat` command prints the contents of a file to standard output, but we can also use it to redirect the content of the file into another file,
- `echo "Results for crt.sh:" >> $DIRECTORY/report` and `jq -r ".[] | .name_value" $DIRECTORY/crt >> $DIRECTORY/report` extract domain names from the `crt.sh` report and append it to the end of the `report` file.

## Scanning Multiple Domains

To write a tool that can scan *multiple domains* with a single command, like this:
```
./recon.sh facebook.com fbcdn.net nmap-only
```
The `getopts` tool parses options from the command line by using singlecharacter flags.

*OPTSTRING* specifies the option letters that `getopts` should recognize:
  -  if it should recognize the options `-m` and `-i`, you should specify `mi`,
  -  if you want an option to contain argument values, the letter should be followed by a colon, like `m:i`.

*NAME* argument specifies the variable name that stores the option letter. 
```
getopts OPTSTRING NAME
```

The `getopts` tool also automatically stores the value of any options into the `$OPTARG` variable. We can store that value into a variable named `MODE`:
```
getopts "m:" OPTION
MODE=$OPTARG
```
- `-m` flag to specify the scan mode and assume that all other arguments are domains.

#### `getopts` stops parsing arguments when it encounters an argument that doesn’t start with the - character.
So you’ll need to place the scan mode *before* the domain arguments:
```
./recon.sh -m nmap-only facebook.com fbcdn.net
```

### Loop

Bash has two types of loops:
- `for` - works better for our purposes, as we already know the number of values we are looping through (you already have a list of values to iterate through),
- `while` - when you’re not sure how many values to loop through but want to specify the condition in which the execution should stop.

Syntax of a `for`, for every item in *LIST_OF_VALUES*, bash will execute the code between `do` and `done` once:
```
for i in LIST_OF_VALUES
do
 DO SOMETHING
done
```

Now let’s implement our functionality:
```
for i in "${@:$OPTIND:$#}"
do
 # Do the scans for $i
done
```

- `"${@:$OPTIND:$#}"` that contains every command line argument, besides the ones that are already parsed by `getopts`, which stores the index of the first argument after the options it parses into a variable named `$OPTIND`,
- `"${@:OPTIND:}"` slices the array so that it removes the *MODE* argument (like `nmap-only`) making sure that we iterate through only the domains part of our input,
- `$@` represent the array containing all input arguments,
- `$#` is the number of command line arguments passed in,
- `$i` variable represents the current item in the argument array.

In bash, you can *slice arrays* by using this syntax:
```
"${INPUT_ARRAY:START_INDEX:END_INDEX}"
```

We can then wrap the loop around the code:
```
#!/bin/bash
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
nmap_scan()
{
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
}
dirsearch_scan()
{
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
}
crt_scan()
{
 curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
 echo "The results of cert parsing is stored in $DIRECTORY/crt."
}
getopts "m:" OPTION
MODE=$OPTARG

for i in "${@:$OPTIND:$#}"
do

 DOMAIN=$i
 DIRECTORY=${DOMAIN}_recon
 echo "Creating directory $DIRECTORY."
 mkdir $DIRECTORY

 case $MODE in
  nmap-only)
   nmap_scan
   ;;
  dirsearch-only)
   dirsearch_scan
   ;;
  crt-only)
   crt_scan
   ;;
  *)
   nmap_scan
   dirsearch_scan
   crt_scan
   ;;
 esac
 echo "Generating recon report for $DOMAIN..."
 TODAY=$(date)
 echo "This scan was created on $TODAY" > $DIRECTORY/report
  if [ -f $DIRECTORY/nmap ];then
  echo "Results for Nmap:" >> $DIRECTORY/report
  grep -E "^\s*\S+\s+\S+\s+\S+\s*$" $DIRECTORY/nmap >> $DIRECTORY/report
 fi
  if [ -f $DIRECTORY/dirsearch ];then
  echo "Results for Dirsearch:" >> $DIRECTORY/report
  cat $DIRECTORY/dirsearch >> $DIRECTORY/report
 fi
  if [ -f $DIRECTORY/crt ];then
  echo "Results for crt.sh:" >> $DIRECTORY/report
  jq -r ".[] | .name_value" $DIRECTORY/crt >> $DIRECTORY/report
 fi
 done
```
- loop starts with the `for` keyword and ends with the `done` keyword,
- we check whether the output file of an `nmap` scan (`if [ -f $DIRECTORY/nmap ];then`), a `dirsearch` scan (`if [ -f $DIRECTORY/dirsearch ];then`), or a `crt.sh` scan (`if [ -f $DIRECTORY/crt ];then`) exist so we can determine if we need to generate a report for that scan type.

The brackets `[]` mean that we’re passing the contents into a test command: 
- `[ -f $DIRECTORY/nmap ]` is equivalent to `test -f $DIRECTORY/nmap`. The `test` command evaluates a conditional and outputs either `true` or `false`.
- `-f` flag tests whether a file exists.

Some useful test conditions:
- `-eq` and `-ne` flags test for *equality* and *inequality*, respectively.

This returns `true` if `$3` is *equal* to `1`:
```
if [ $3 -eq 1 ]
```

This returns `true` if `$3` is *not equal* to `1`:
```
if [ $3 -ne 1 ]
```
- `if [ $3 -gt 1 ]` - test for greater than,
- `if [ $3 -ge 1 ]` - test for greater than or equal to,
- `if [ $3 -lt 1 ]` - test for less than,
- `if [ $3 -le 1 ]` - test for less than or equal to,
- `if [ -z "" ]` and `if [ -n "abc" ]` - test whether a string is empty. These conditions are both true.

The `-d`, `-f`, `-r`, `-w`, and `-x` flags check for directory and file statuses. 

- `if [ -d /bin]` - returns `true` if `/bin` is a *directory* that exists,
- `if [ -f /bin/bash ]` - returns `true` if `/bin/bash` is a *file* that exists,
- `if [ -r /bin/bash ]` - returns `true` if `/bin/bash` is a *readable* file,
- `if [ -w /bin/bash ]` - or a *writable* file,
- `if [ -x /bin/bash ]` - or an *executable* file.

You can also use `&&` and `||` to combine test expressions. 
- `if [ $3 -gt 1 ] && [ $3 -lt 3 ]` - returns `true` if *both* expressions are `true`,
- `if [ $3 -gt 1 ] || [ $3 -lt 0 ]` - returns `true` if *at least one of them* is `true`.

Find more comparison flags in the `test` command’s manual by running `man test` (you can always enter `man` followed by the command name).

## Writing a Function Library

As your codebase gets larger, you should writing a *function library* to *reuse code*. 

We can store all the commonly used functions in a separate file `scan.lib`. 

We can call these functions as needed for future recon tasks:
```
#!/bin/bash
nmap_scan()
{
  nmap $DOMAIN > $DIRECTORY/nmap
  echo "The results of nmap scan are stored in $DIRECTORY/nmap."
}
dirsearch_scan()
{
  $PATH_TO_DIRSEARCH/dirsearch.py -u $DOMAIN -e php --simple-report=$DIRECTORY/dirsearch
  echo "The results of dirsearch scan are stored in $DIRECTORY/dirsearch."
}
crt_scan()
{
  curl "https://crt.sh/?q=$DOMAIN&output=json" -o $DIRECTORY/crt
  echo "The results of cert parsing is stored in $DIRECTORY/crt."
}
```

We source a script via the `source` command, followed by the path to the script:
```
#!/bin/bash
source ./scan.lib
PATH_TO_DIRSEARCH="/Users/vickieli/tools/dirsearch"
getopts "m:" OPTION
MODE=$OPTARG
for i in "${@:$OPTIND:$#}"
do
 DOMAIN=$i
 DIRECTORY=${DOMAIN}_recon
 echo "Creating directory $DIRECTORY."
 mkdir $DIRECTORY
 case $MODE in
  nmap-only)
   nmap_scan
   ;;
  dirsearch-only)
   dirsearch_scan
   ;;
  crt-only)
   crt_scan
   ;;
  *)
   nmap_scan
   dirsearch_scan
   crt_scan
   ;;
 esac
 echo "Generating recon report for $DOMAIN..."
 TODAY=$(date)
 echo "This scan was created on $TODAY" > $DIRECTORY/report
 if [ -f $DIRECTORY/nmap ];then
   echo "Results for Nmap:" >> $DIRECTORY/report
   grep -E "^\s*\S+\s+\S+\s+\S+\s*$" $DIRECTORY/nmap >> $DIRECTORY/report
 fi
 if [ -f $DIRECTORY/dirsearch ];then
   echo "Results for Dirsearch:" >> $DIRECTORY/report
   cat $DIRECTORY/dirsearch >> $DIRECTORY/report
 fi
 if [ -f $DIRECTORY/crt ];then
   echo "Results for crt.sh:" >> $DIRECTORY/report
   jq -r ".[] | .name_value" $DIRECTORY/crt >> $DIRECTORY/report
 fi
done
```

## Building Interactive Programs
