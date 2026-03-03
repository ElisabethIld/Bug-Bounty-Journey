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
