## Input/ Output Redirection

STDINPUT - 1

STDOUTPUT- 2

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

**`double quote` - Variable preceded by a dollar sign are specially treated but wildcards and single quotes lose special meaning**

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

#### 