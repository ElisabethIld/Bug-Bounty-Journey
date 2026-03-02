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



