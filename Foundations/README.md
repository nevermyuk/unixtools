## Input/ Output Redirection

STDINPUT - 0

STDOUTPUT- 1

STDERROR - 2



#### Cat

- Concatenate

```
cat -n  #number lines of input
```



#### BC

- basic calculator

```
bc
50 + 50
100
x=20

```

## Files and Directories

`Relative Path`

```bash
pwd  # print working dir
mkdir a # create a dir named a
cd a # change directory into a
cd .. # move up one level
cd ../.. #move up 2 level.
cd #moves to home dir
cd /home/usr #move to home dir by abs path 
```

#### Root directory

`bin` - Binary. Essential commands stored.

`boot`- File related to system operation at startup

`dev` -  Special dir corresponding to devices of the system. Can access terminal,disks etc.

`etc` - dir with admin information associated with system

`home` - private file of user

`lib` - library maintains data files associated with various commands

`lost and found` - Special dir used during disk maintenance to store files that are found without proper linkage to other dir.

`mnt` - Mount. Used for mounting external storage devices. e.g USB

`proc` - contain info about system

`sbin` - system binaries, essential file used by sysadmins.

`tmp` - gather tmp file of system. Temporary and deleted after words.

#### Usr Directory

`usr` - User storing additional commands in libraries, not essential for booting up system.

**Inside /usr/**

`games` - with some console games

`include` - include directory with headers of C and C++ programs.

`lib` - additional libraries

`local` - local directory that maintains info about locally installed cmd

`sbin` - System binaries.

`share` - text and documentation that can be shared among diverse system independent of architecture

`src` - store source code of various commands and libraries.

```bash
rm # remove a file
mv hello HELLO # change file name
mkdir hell # make hell dir
mv HELLO hell/HEL # move hello into hell dir and rename to HEL
cp hell/HEL . #copy file to current directory
cp -r hell/ . #copy all files to current directory - recursively.
rmdir hell # remove directory will fail if multiple files.
rm -rf hell #remove non-empty dir recursive and force flags
# move all files from current folder, but not subfolder into ./Week1
find ./ -maxdepth 1 -type f -exec mv -t ./Week1 \{\} \+

```

[man Find](http://man7.org/linux/man-pages/man1/find.1.html)

## Command Line Argument

##### Wildcard Expansion

```bash
# Wildcards
echo * # match all files
echo store* # match file beginning with store
echo *.? # match file ending in . with a single character
echo store*.[ab] #match file ending in .a or .b
echo store*.[a-z] #match file ending in . followed by lowercase letter
echo store*.[^b] #match file ending in . follewed by anything but b
```

#### Shell variables

```bash
name=John
surname=Doe
echo $name # Expand variable - John
echo $PATH # Built-in variable (executable file path): Separated by colon
echo $HOME # Built-in variable(home dir path)
echo Keep up with ${surname} # Expand variable - Keep up with the Does
read a b c d 
Live free or die! # Read into variables four words
echo $a $b $c $d # Prints Live free or die
echo This book costs $32.99 # Unwanted expansion , $3 treated as variable
```

#### Quoting

```bash
echo 'This book costs $32.99'  # Protect content   - single quote
echo This book costs \$32.99   # Protect single character
echo "*** Meet $name's father ****" # Expands variable
# Expand and protect content by escaping double quote and variable expansion. \"Hell\" and \$4
echo "$name \"Hell\" $surname received \$4.99"
# Backslash not specially treated in ''
echo 'No backslashes (\) here. Go out of ' \' ' and use them.'
```

**`double quote` - Variable preceded by a dollar sign are specially treated but Wildcards and single quotes lose special meaning**

#### Multi-line

```bash
echo 'hello
ending here '
```

#### Command Expansion

Command output as another argument to another command

```bash
echo "Today is $(date)"
Today is Sun Apr 12 14:08:25 +08 2020
start=$(date +%s)
1586671740
end=$(date +%s)
echo $end
1586671787
echo "The process lasted $(expr $end - $start) seconds"
The process lasted 47 seconds
```

#### Here Documents

The ability of the shell to specify the standard input for a command after its invocation.

This input forms what is called a Here document.

A common way to specify a here document is using a double less than sign < <,followed by a backslash /

and then a sequence of characters that will be used

```bash
cat -n <<\EOF
> Hello world
> I am around
> Do you see me
> EOF
     1  Hello world
     2  I am around
     3  Do you see me


cat <<end-of-message
> From: Darth Vadar <Anakin.Skywalker@death-star.gov>
> To: Child Support <info@csa.gov>
> Date: $(date -R)
> Remove me from your distribution list. -D.
> end-of-message

From: Darth Vadar <Anakin.Skywalker@death-star.gov>
To: Child Support <info@csa.gov>
Date: Sun, 12 Apr 2020 14:15:57 +0800

Remove me from your distribution list. -D.
```

### Pipeline

#### Command Grouping

```bash
ls /etc > files

wc < files # Count lines, words, characters
186  186 1785

wc -l < files  # Count lines

ls /etc | wc -l # piping output of ls into wc
186
ls /usr | cat -n # With line number
   	 1  bin
     2  games
     3  include
     4  lib
     5  libexec
     6  local
     7  sbin
     8  share
     9  src
```

##### Exit Status

```bash
echo $? # Print exit status (zero)
wc xyzzy
wc: xyzzy: No such file or directory
echo $? # Print exit status (non-zero) , if not zero means fail.
1
```

##### **Examples**

```bash
touch a-file
cat -n a-file > b-file && rm a-file # Execute second only if first succeeds.
# So a-file will be removed since b-file succesfully created.
cat -n bfile >cfile ** rm b-file # bfile typo; 2nd command will fail 
```

##### Disjunction Operator

`OR || - A OR B`  - Only run 2nd command if first fails.

```bash
touch a-file
cp a-file b-file || echo Copy failed  # Execute if did not succeed
cp afile bfile || echo Copy failed
cp: cannot stat 'afile': No such file or directory
Copy failed
```

##### Negation Operator

`Exclamation Mark !` - 

```bash
! date # Command succeeds
echo $? # Print 1 because first command succeed with exit code 0. Negation makes it 1.
1
! ls xyzzy # Command fails
ls: cannot access 'xyzzy': No such file or directory
echo $?  # Print 0, command fails so exit code 1 becomes 0.
0 
```

##### Grouping Commands

`Semi-colon ;` - Put commands on the same line

`Curly brackets {}` -  Put two commands in curly brackets group them and  to redirect output of command group together.

```bash
echo 'Today is: ' ; date
Today is: 
Sun Apr 12 16:30:25 +08 2020

# -n flag means do not append a newline
echo -n 'Today is: ' ; date # Execute two commands in a row 
Today is: Sun Apr 12 16:20:58 +08 2020
echo 'Today is: ' ; date > timestamp # Execute two commands, but only date will be 											   redirected to timestamp file
cat timestamp
Sun Apr 12 16:23:36 +08 2020

{ echo -n 'Today is: ' ; date ; } > timestamp # redirecting output to timestamp file
cat timestamp
Today is: Sun Apr 12 16:21:53 +08 2020

{ echo -n 'Today is: '; date; } | wc -c # Pipe output of both to wc
39

{             # Multi-line command by starting with curly bracket
> echo -n 'Today is: '
> date
> }           # Only executed if closed.
Today is: Sun Apr 12 16:26:37 +08 2020
```

### Quiz

Output the same as the command `ls /etc | cat -n`

```bash
ls /etc >/tmp/ls ; cat -n </tmp/ls
```

Consider the following command.

```bash
{ ls /tmp/x && rm /tmp/x ; } 
{ ls /tmp/x || touch /tmp/x ; }
```

Which of the following will be true after the command is executed?

- The old file `/tmp/x` will be deleted, if it exists.

  - If `x` doesn't exist, `ls` will cause an error and as such the `rm` command will not be executed.
  - If `x` already exists, `rm` command will be executed and will remove the file.

- A new file named `/tmp/x` will always be created. 

  - Since `x` doesn't exist, `ls` command will fail, as a result the `touch` command will be executed and create the file.

    

## Scripts

```bash
cat >paris-time
#!/bin/sh  # shebang
sh paris-time # invoke shell to run script
chmod +x paris-time # make script executable
./paris-time  # run it by specifying path
PATH=$PATH:. # add current dir to path
paris-time
mkdir $HOME/bin #create dir for executables
PATH=$PATH:$HOME/bin # add dir to path , all files in bin can be	 						executed from any other dir.
```

```bash
cat >any-time
#!/bin/sh
echo -n "The time in $1 is " # Use first argument
TZ=$1 date
sh any-time Europe/Amsterdam
The time in Europe/Amsterdam is Sun Apr 12 16:05:06 CEST 2020


```

#### Shell Functions

Functions . Maintained during shell's session

**If you want to set variables/functions for all shell, add to .bashrc** 

```bash
 showtime() {  
                local TZIN=$1; 
                local TZOUT=$2; 
                local TIME=$3; 
                echo -n "$TZIN $TIME is $TZOUT "; 
                TZ=$TZOUT date --date="TZ=\"$TZIN\" $TIME"; 
 			}
 			
$ showtime America/New_York Asia/KolKata 11:45
America/New_York 11:45 is Asia/KolKata Sun Apr 12 15:45:00 Asia 2020
```

#### Why the #!/bin/sh Shebang is required

```bash
When a script file starts with #!, then the Unix kernel can load and execute it (via the specified interpreter, e.g. /bin/sh) as if it was a compiled executable file.

In addition, some shells employ a heuristic whereby if the kernel cannot execute a file marked as executable, they try to run it as a shell script.

For example, tracing the execution of sh -c hi, where hi starts with #!/bin/sh results in the following exec system calls.

27093 execve("/bin/sh", ["sh", "-c", "hi"], 0x7fffc6e28ec0 /* 38 vars */) = 0
27094 execve("./hi", ["hi"], 0x555748cc6b48 /* 38 vars */) = 0
On the other hand, if the first line is missing, then the execution goes like this.

27111 execve("/bin/sh", ["sh", "-c", "hi"], 0x7fff5cb29500 /* 38 vars */) = 0
27112 execve("./hi", ["hi"], 0x5608a2cb2b48 /* 38 vars */) = -1 ENOEXEC (Exec format error)
27112 execve("/bin/sh", ["/bin/sh", "./hi"], 0x5608a2cb2b48 /* 38 vars */) = 0
Notice how the second exec fails and then the shell invokes itself a shell to run the file as a shell script.
```

