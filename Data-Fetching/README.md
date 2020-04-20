# [Data](https://git-scm.com/book/en/v2) Fetching

## Data From the Web ( CURL )

#### Fetch Document from Web

```bash
Fetch documents from the web
$ curl -s --compressed http://www.gutenberg.org/cache/epub/2701/pg2701.txt |
> head
The Project Gutenberg EBook of Moby Dick; or The Whale, by Herman Melville

This eBook is for the use of anyone anywhere at no cost and with
almost no restrictions whatsoever.  You may copy it, give it away or
re-use it under the terms of the Project Gutenberg License included
with this eBook or online at www.gutenberg.org


Title: Moby Dick; or The Whale

$ curl -s --compressed http://www.gutenberg.org/cache/epub/2701/pg2701.txt |
> wc
  22108  215135 1257296
$

```

#### Fetch Data from Web

```bash
$ curl -s "http://api.currencylayer.com/\
> live?access_key=$API_KEY&source=USD&currencies=EUR" | # Obtain USD EUR rate
> jq . # Format the JSON result
{
  "success": true,
  "terms": "https://currencylayer.com/terms",
  "privacy": "https://currencylayer.com/privacy",
  "timestamp": 1584462846,
  "source": "USD",
  "quotes": {
    "USDEUR": 0.91095
  }
}
```

#### Query Json Data

```bash
$ WGE='https://www.wikidata.org/w/api.php?action=wbgetentities&format=json' # API endpoint
$ curl -s "$WGE&titles=Moon&sites=enwiki" | # Obtain the Moon identifier
> jq -r '.entities[].id' # Query the JSON data for the identifier
Q405
$ MOONID=$(curl -s "$WGE&titles=Moon&sites=enwiki" | # Store the result in a variable
> jq -r '.entities[].id')
$ curl -s "$WGE&ids=$MOONID" | # Obtain Moon's data
> jq -r .entities.$MOONID.claims.P2067[].mainsnak.datavalue.value.amount # Query mass
+73.477

```

#### Query relational database

```bash
$ echo 'SELECT COUNT(*) FROM projects' | # SQL query
> mysql -ughtorrent -p ghtorrent # MySQL client
Enter password:        
count(*)
16331225

```

#### Commands for Each Database

```bash
$ sqlplus #oracle
$ osql # Microsoft SQL Server
$ sqlite3 # SQLite engine
$ psql #PostgreSQL
$ odbc # Any ODBC source
```

#### Putting it all together

```bash
$ echo 'select url from projects limit 3' | # Obtain URL of first three projects
> mysql -ughtorrent -p ghtorrent | # Invoke MySQL client
> while read url ; do
>   curl -s $url | # Fetch project's details
>     # Print owner, project, and last push time
>     jq -r '{owner: .owner.login, name: .name, pushed: .pushed_at}'
> done
```

#### Query LDAP stores

Lightweight Directory Access Protocol

```bash
$ curl ldap://ldap.satrapade.com:389/ou=People,dc=satrapade,dc=com\
> '?cn?sub?(&(objectClass=person)(uid=dds))'
```

## Data from Archives

#### Archive Listing

```bash
$ curl -Ls https://git.kernel.org/torvalds/t/linux-5.6-rc2.tar.gz |
> tar -tzvf - |
> head -10
```

#### Extract Files

```bash
$ curl -Ls https://git.kernel.org/torvalds/t/linux-5.6-rc2.tar.gz |
> tar -xzf -
```

#### Create Archive

```bash
$ tar -cf share.tar /usr/share # Create archive from files
tar: Removing leading `/' from member names
$ tar -cf - /usr/share | # Create archive to stdout
> gzip -c >share.tar.gz # Compress
tar: Removing leading `/' from member names
$ ls -lh # See file size (in human-readable units)
-rw-r--r-- 1 dds dds 342M Feb 20 15:34 share.tar.gz
$ du -sh /usr/share # Obtain directory size
911M    /usr/share
$ tar -czf share.tar.gz /usr/share # Shorthand for gzip compression
tar: Removing leading `/' from member names
$
```

#### Connect Archive Commands

```bash
$ ls /usr/share/dict
$ mkdir dict2
$ (
>   cd /usr/share/dict
>   tar -cf - .
> ) | (
>   cd dict2
>   tar -xf -
> )
$ ls dict2
$ tar -C /usr/share/dict -cf - . | tar -C dict2 -xf -
```

#### List Library Contents

```
$ ar tv /usr/lib/x86_64-linux-gnu/libc.a |
> head
```

#### List jar file Contents

```bash
$ jar tvf /home/dds/hadoop-2.5.0/share/hadoop/tools/lib/junit-4.11.jar |
> head
```

#### List zip file Contents

```bash
$ unzip -v jpf/Calculator.zip |
> head
```

#### Compress data

```bash
$ gzip -dc <share.tar.gz | bzip2 -c >share.tar.bz2
```

### Quiz

```bash
Which of the following commands will copy the directory /home/mary/run to /home/mary/work?
$ tar -C /home/mary -cf - run | tar -C /home/mary/work -xf -

$ (cd /home/mary ; tar -cf - run) | (cd /home/mary/work ; tar -xf -)

$ (cd /home/mary/run ; tar -cf - .) | (mkdir /home/mary/work/run ; cd /home/mary/work/run ; tar -xf -)

$ (cd /home/mary ; tar -cf - run) | (tar -C /home/mary/work -xf -)

$ tar -C /home/mary -cf /tmp/run.tar run ; tar -C /home/mary/work -xf /tmp/run.tar

```

```bash
Which of the following commands will create a compressed file with the output of the ls command?
$ ls -lR /home/joe | gzip -c >ls.out.gz
$ ls -lR /home/joe >ls.out ; gzip ls.out
$ ls -lR /home/joe | bzip2 -c >ls.out.bz2
$ ls -lR /home/joe >ls.out ; bzip2 ls.out

Piping the output of ls to gzip or bzip2 with the -c argument and then redirecting their output to the desired file is the best choice. Another choice that works involves redirecting the output of ls into a temporary file and then compressing that file by passing it as an argument to gzip or bzip2.
```

## Data from Remote Hosts

#### Create private/public key pair

```bash
$ cd 
$ mkdir .ssh # Create directory for your ssh keys
$ cd .ssh
$ ssh-keygen # Create public and private key pair
Generating public/private rsa key pair.
Enter file in which to save the key (/home/dds/.ssh/id_rsa):
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/dds/.ssh/id_rsa.
Your public key has been saved in /home/dds/.ssh/id_rsa.pub.
$ cat id_rsa.pub # Obtain your public key
```

#### Log into Remote host

```bash
$ cd 
$ mkdir -p .ssh # Create directory for your ssh keys
$ cd .ssh
$ cat >>authorized_keys # Add key to those with allowed access
ssh-rsa 
EXAMPLE KEY
Examplekey.com
$ sshexample.com hostname # On the 2nd host execute command on main
example.com
$
```

#### Configure ssh for quick access to the same host

```bash
$ ssh -i ~/.ssh/ec2_id_rsa ec2-user@ec2-54-81-102-239.compute-1.amazonaws.com cat /etc/motd
$ cat >>~/.ssh/config <<\EOF
Host aws
HostName ec2-54-81-102-239.compute-1.amazonaws.com
User ec2-user
IdentityFile ~/ec2_id_rsa

EOF
$ ssh aws cat /etc/motd
```

#### use ssh

```bash
$ hostname
$ date
$ ssh anotherhost.example.com
$ hostname # another host
$ date
$ ssh example.com cat /etc/motd # Run a command back 1st host
$ ssh example.com cat /etc/passwd | wc -l # Count users
```

#### Pipe through ssh

```bash
$ tar -czf - work-directory | # Pack directory to standard output
> ssh backup-server dd of=/dev/st0 bs=1M # Send data to a remote tape
$ ssh backup-server dd if=/dev/st0 bs=1M | # Obtain data from a remote tape
> tar -xzf - # Unpack files from standard input
$ tar -czf - work-directory |	# Pack directory to standard output
> ssh otherhost tar -xzf - # Unpack files from standard input
$
```

#### Traverse Firewall

```bash
$ curl ldap://ldap.example.com:389/ou=People,dc=example,dc=com'?cn?sub?(&(objectClass=person)
> (uid=dds))'
curl: (7) Failed to connect to ldap.example.com port 389: Connection timed out # Not allowed on firewall
$ ssh -f -L 8389:ldap.example.com:389 shell.example.com sleep 9999 # Connect thru shell.example.com
$ curl ldap://localhost:8389/ou=People,dc=example,dc=com'?cn?sub?(&(objectClass=person)(uid=dds))'
DN: uid=dds,ou=People,dc=example,dc=com
        cn: Example
        
 $ ps x
  PID TTY      STAT   TIME COMMAND
24182 ?        S      0:00 sshd: dds@pts/1
24183 pts/1    Ss     0:00 -bash
27803 ?        Ss     0:00 ssh -f -L 8389:ldap.example.com:389 shell.example.com sleep 9999
27827 pts/1    R+     0:00 ps x
$ kill 27803
```

#### Synchronize files

```bash
$ rsync -av host.example.com:/usr/share/sounds/alsa/ wav-files # Mirror remote
$ rsync -av host.example.com:/usr/share/sounds/alsa/ wav-files # Refresh
```

### Quiz

```bash
When you create a public/private key pair for remote shell access you:

Store the private key in your local host and the public key in the remote host

Which of the following commands can utilize a private/public key pair to access a remote host?
git, rsync,scp,ssh

Which of the following commands will create a copy of the local file a on the remote host trantor?
$ scp a trantor:
$ tar -cf - a | ssh trantor tar -xf -
$ rsync ./a trantor:
$ ssh trantor dd of=a <a
```

## Data from Local Filesystems

#### Recurse Directories with Find

```bash
$ cd /usr
$ find . | head # List all entries under the current directory
```

#### Limit listing to files

```bash
$ find . -type f | head # List only files
```

#### Limit listing to directories

```bash
$ find . -type d | head # List only directories
```

#### Find files by name

```bash
$ find . -name '*.a' | head # List entries ending in .a

```

#### Combine Options

```bash
$ find . -name test # List entries named test

```

#### Act on it

```bash
$ find . -type f -ls | head # List details of each entry
```

#### Execute command for each file

```bash
$ find /usr/share/doc -type f -name changelog.gz -exec gzip -dc '{}' \; |
> wc -l
```

#### Putting it all together

```bash
$ find /usr/include -type f | head -5
$ find /usr/include -name '*c++*' -prune -o -type f -print | head # Skip c++ entries

```

### Quiz

```bash
Which of the following commands will recursively list all entries under the /usr/share directory?
$ find /usr/share -print
Which of the following commands will recursively list all plain files (not directories) under the /usr/share directory?
$ find /usr/share -type f -print

Your current directory is /usr/include; the location of C header files. These by convention have a .h suffix. Which command is the safest option to recursively list all header regular files named with a .h suffix?
$ find . -name '*.h' -type f
```

## Data from Version Control System

#### Repo Fetching

```bash
$ git clone https://github.com/torvalds/linux.git # Clone the Linux repo
$ cd linux
$ git log v5.6-rc2 | head -12 # List commits
```

#### Commit Logs

```bash
$ git log --pretty=format:%an | # List author name of each commit
> grep -v Torvalds | # Omitting Linus Torvalds
> head # First ten
```

#### Process Commit Logs

```bash
$ git log --pretty=format:%ae | # List each commit's author email
> sort | # Bring emails together
> uniq -c | # Count occurrences
> sort -rn | # Order by number
> head # List first ten
```

#### Obtain details for each file

```bash
$ find kernel -type f -print | # Find kernel source code files
> while read f ; do # For each file
>   echo -n "$f " # Print its name on a single line
>   git log --oneline $f | wc -l # Count number of changes
> done |
> head -3 # Print first three entries


 $find kernel -type f -print |
> while read f ; do
>   echo -n "$f "
>   git log --oneline $f | wc -l
> done |
> sort -k 2nr | # Sort by the second field in reverse numerical order
> head
```

#### Obtain detail for each line

```bash
$ git blame kernel/fork.c | sed -n 20,40p # List attribution of lines 20-40
```

#### Process blame details

```bash
$ git blame --line-porcelain kernel/fork.c | head -15
```

#### Who knows best this file?

```bash
$ git blame --line-porcelain kernel/fork.c | # Obtain line metadata
> grep '^author ' | # Show each line's author
> sort | # Order by author
> uniq -c | # Count author instances
> sort -rn | # Order by count
> head # Show first ten
```

#### Average age of file's line

```bash
$ date +%s # Show date in seconds since Epoch (1/1/1970)
$ date -d @1582286183 # Print the date for this number of Epoch seconds
$ date -d @0 # Print the date at Epoch

$ date -d @$( # Print the date corresponding to the output of:
  git blame --line-porcelain kernel/fork.c |
  awk '/^author-time / {sum += $2; count++} # Maintain sum and count of commit times
    END {printf("%d", sum / count)}' # Print mean authorship time
)
```

#### Track file size growth

```bash
$ file=drivers/thermal/of-thermal.c # File to examine
$ git log --pretty=format:%H -n 3 $file # Show SHA of commits
285249884ec19480f99c011d223760bb1fc865a2
17e8351a77397e8a83727eb17e3a3e9b8ab5257a
$ git log --pretty=format:%H $file | # Obtain commits' SHA
> while read sha ; do # For each SHA
>   git show $sha:$file | # List file state at that commit
>   wc -l # Count number of lines
> done |
> head -15 # First 15 entries
```

### Quiz

```bash
Create a local clone of the Git repository https://github.com/dspinellis/UMLGraph.git.
Using command-line tools obtain how many commits were made to the repository's master branch by Andrea Aime.

$ git log --pretty=format:%an | sort | uniq -c | sort -rn

Using command-line tools find the name of the Java source code file that has undergone most changes.

$ git log --pretty=format: --name-only | sort | uniq -c | sort -rg | head -10

What was the date on which the earliest surviving line of file src/main/java/org/umlgraph/doclet/StringUtil.java was written?

$ git blame HEAD src/main/java/org/umlgraph/doclet/StringUtil.java

How many characters were in the file src/main/java/org/umlgraph/doclet/StringUtil.java in version d0ede46da8aaff13bccd1f2b3fd201bb383d68f9?
$ git show d0ede46da8aaff13bccd1f2b3fd201bb383d68f9:src/main/java/org/umlgraph/doclet/StringUtil.java | wc -c

```

[Finding most changed file](https://stackoverflow.com/questions/7686582/finding-most-changed-files-in-git)

## 

## Data from Compiled Code

#### List object file contents

```bash
$ cat hello.c # List example C file
$ cc -c hello.c # Compile the file into object format
$ nm hello.o # List symbols in object file
                 U fprintf
0000000000000004 C global
0000000000000000 b local
0000000000000000 t localfun
0000000000000006 T main
                 U stdout
```

#### List executable file contents

```bash
$ cc -o hello hello.c # Compile and link file into executable
$ nm hello | head # List first ten symbols
0000000000600990 B __bss_start
0000000000600998 b completed.6661
0000000000600980 D __data_start
0000000000600980 W data_start
0000000000400480 t deregister_tm_clones
0000000000400500 t __do_global_dtors_aux
0000000000600768 t __do_global_dtors_aux_fini_array_entry
0000000000600988 D __dso_handle
0000000000600778 d _DYNAMIC
0000000000600990 D _edata
$ strip hello # Remove symbols
$ nm hello # Attempt to list symbols
nm: hello: no symbols
$ nm /bin/ls # Attempt to list symbols
nm: /bin/ls: no symbols    # Strip to save disk space
```

#### File with most global variables

```bash
$ nm -o $(find . -name \*.o) | head -3 # Run nm on all .o files
./modules/standard/mod_dir.o:00000000 t add_index
./modules/standard/mod_dir.o:         U ap_construct_url
./modules/standard/mod_dir.o:         U ap_destroy_sub_req
$ nm -o $(find . -name \*.o) |
> grep ' D ' | head -3 # Obtain only uninitialized global data definitions
./modules/standard/mod_dir.o:00000000 D dir_module
./modules/standard/mod_actions.o:00000000 D action_module
./modules/standard/mod_auth.o:00000000 D auth_module
$ nm -o $(find . -name \*.o) |
> grep ' D ' |
> cut -d: -f 1 | head -3 # Get file name
./modules/standard/mod_dir.o
./modules/standard/mod_actions.o
./modules/standard/mod_auth.o
$ nm -o $(find . -name \*.o) |
> grep ' D ' |
> cut -d: -f 1 |
> sort | uniq -c | sort -rn | head -5 # Order by number of global variables
     37 ./main/http_main.o
      2 ./modules.o
      2 ./main/http_config.o
      1 ./modules/standard/mod_userdir.o
      1 ./modules/standard/mod_status.o
```

#### Find shared libraries

```bash
$ ldd /bin/ls # List dynamically linked libraries
linux-vdso.so.1 (0x00007ffe99964000)
$ ldd /bin/* | # Obtain shared libraries of all files
> sed 's/.*=>//;s/^\s*//;s/ .*//' | # Get absolute library path
> sort | uniq -c | sort -rn | head # Count
    108 linux-vdso.so.1 # used most
    108 /lib/x86_64-linux-gnu/libc.so.6
    108 /lib64/ld-linux-x86-64.so.2
     32 /lib/x86_64-linux-gnu/libdl.so.2
```

#### List Java compiled files

```bash
$ javap -cp antlr-3.4-complete.jar org/antlr/runtime/ANTLRFileStream
```

#### List Windows compiled files

```bash
$ cd /cygdrive/c/Windows/
$ dumpbin /imports notepad.exe | head -20 # List imported symbols
$ cd /cygdrive/c/Windows/System32
$ dumpbin /exports wsock32.dll | head -22 # List exported symbols
```

### Quiz

```bash
Which one of the (file - tool) pairings is correct?
/bin/ls - ldd, /bin/ls - nm, app.exe - dumpbin, Test.class - javap, ls.o - nm, app.obj - dumpbin, zlib.dll - dumpbin

Which of the following symbols are defined in the file https://www.spinellis.gr/unix/data/a.out?

$ curl -Ls https://www.spinellis.gr/unix/data/a.out > nm.o
$ nm nm.o
```

## Data from Graphical Desktop System

#### Obtain Windows clipboard data

```bash
$ fmt -s /dev/clipboard
$ tr 'A-Za-z' 'N-ZA-Mn-za-m' </dev/clipboard >/dev/clipboard
```

#### Obtain other clipboard data

```bash
$ pbpaste | # Under macOS 
> tr 'A-Za-z' 'N-ZA-Mn-za-m' |
> pbcopy
$ xsel --clipboard | # On the X Window System
> tr 'A-Za-z' 'N-ZA-Mn-za-m' |
> xsel --clipboard
```

#### File properties on macOS

```bash
$ mdls study.docx  | tail -26 # Obtain a document's properties (macOS)
```

#### Find macOS files by properties

```bash
$ mdfind "kMDItemAuthors == 'John Doe'" # Find documents by property
```

#### Handle Cygwin Paths

```bash
$ cygpath -w /tmp # Convert Cygwin path to Windows format
$ cygpath 'C:\Windows' # Convert Windows path to Cygwin format
$ echo $APPDATA # Location of user's documents
$ du -sh $APPDATA # Pass Windows path to Cygwin tool
```

#### Launch Document

```bash
$ cd ~/Desktop # Navigate to the Desktop
$ wget -q http://www.gutenberg.org/files/98/old/2city12p.pdf
$ cygstart 2city12p.pdf # Launch specified file under Cygwin
$ open 2city12p.pdf # Launch specified file under macOS
$ xdg-open  2city12p.pdf # Launch specified file under Gnome
$ kde-open  2city12p.pdf # Launch specified file under KDE
```

## System Administration

#### Unix Administrative files

```bash
$ cd /etc # Location of system configuration files
$ find . 2>/dev/null | wc -l
2989
$ head passwd # User accounts
```

#### List running processes

```bash
$ ps # List running processes
$ ps uw # List running processes with more details
$ ps xuw # Also include background processes
$ ps auxw | head # Obtain information on all running processes

```

### The /proc file system

```bash
$ cd /proc  # info about processes etc.
$ ls -CF | head -5
$ head -5 cpuinfo
```

#### Obtain file details

```bash
$ stat /etc/motd # Output file's details
$ stat -c '%s' /etc/motd # File size
$ stat -c '%Y' /etc/motd # File modification (seconds since Epoch)
1370204982
$ date -d @1370204982 # Format Epoch time
Sun Jun  2 23:29:42 EEST 2013
$ ls -l /etc/motd # Verify Epoch time
-rw-r--r-- 1 root root 286 Jun  2  2013 /etc/motd
$ stat -c '%U' $HOME # File user
user
```

#### Access the Windows registry

```bash
$ cd /proc/registry # Navigate to the registry virtual folder
$ cd 'HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows NT/CurrentVersion/'
$ ls -CF | head # List contents
$ more ProductName # Read installed software value
$ regtool set /HKEY_CURRENT_USER/Software/Cygwin/TestEntry 'Hello there' # Set value
$ more /proc/registry/HKEY_CURRENT_USER/Software/Cygwin/TestEntry # Read value
$ regtool get /HKEY_CURRENT_USER/Software/Cygwin/TestEntry # Read value
```

#### Obtain Windows system Data

```bash
$ find /cygdrive/c/Windows/system32 -maxdepth 1 -type f -name \*.dll | # Find DLL files
> head | # First ten
> while read f ; do
>   wname=$(cygpath -w $f | sed 's/\\/\\\\/g') # Obtain Windows name with double \\
>   wmic datafile where "Name=\"$wname\"" get name, version # Run wmic query
> done |
> grep windows # Filter out blank lines and headers
```

#### List Windows Services

```bash
$ wmic service get name,state | head # List status of Windows services
$ wmic process get name,virtualsize |
> sort -k2rn | # List by reverse numerical order of second field
> head -6 # First six entries
```

#### List Windows Processes

```bash
$ tasklist | head
```

## Generators

#### Generate number sequences

```bash
$ seq 5 
$ for i in $(seq 50) ; do
>   echo -n '.'
> done
$ seq 0.1 4.5
$ seq 0 20 100
```

#### Generate blocks of data

```bash
$ tar cf - /home | ssh tapehost.example.com dd of=/dev/st0 bs=1M
$ dd if=/dev/zero of=1GBfile bs=1M count=1000
$ ls -lh 1GBfile
$ ssh remote.example.com 'nc -l 9999 >/dev/null &'
$ dd if=/dev/zero bs=1M count=10 | nc -w 1 remote.example.com 9999 # good for testing speed
$ curl ftp://ftp.freebsd.org/pub/FreeBSD/releases/amd64/amd64/ISO-IMAGES/\
> 10.3/FreeBSD-10.3-RELEASE-amd64-memstick.img | # Fetch bootable FreeBSD image
> sudo dd of=/dev/sdx bs=10240 # Write it to a pen drive
```

#### Generate repeated strings

```bash
$ yes | head
$ yes no | head -5
$ yes | fsck /dev/sdb2
```

#### Dictionary file

```bash
$ cd /usr/share/dict
$ head words # First ten words in dictionary
$ tail words # Last ten words in dictionary
$ wc -l words # Number of lines (words) in dictionary
```

#### Generate random data

```bash
$ openssl rand 10 | # Generate ten random bytes
> od -d -A n # Convert them into five integers
$ openssl rand 256 | # Generate 256 random bytes
> tr -cd 'a-zA-Z0-9!@#$%*()-_+=:;",./?' | # Keep printable characters
> cut -b1-10 # Print first ten characters
u?L5rK_EK,
```

### Quiz

```bash
How many lines are produced if you create a sequence starting from 7 up to at most 170 in increments of 0.3?
$ seq 7 0.3 170 | wc
Create a file by writing to it with the dd command twelve 32k-size blocks.
$ dd if=/dev/zero of=32KBfile count=12 bs=32K

```



## Resources

- [How To Set Up SSH Keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)
- [Secure shell](https://en.wikipedia.org/wiki/Secure_Shell) 
- [Rsync](https://en.wikipedia.org/wiki/Rsync) 
- [OpenSSH](https://www.openssh.com/) 
- [kill](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/kill.html) 
- [ps](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/ps.html) 
- [nm](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/nm.html) 
- [cut](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/cut.html)
- [grep](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/grep.html) 
- [sed](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/sed.html) 
- [strip](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/strip.html) 
- [Cygwin](https://www.cygwin.com/) 
- [Outwit](https://www.spinellis.gr/sw/outwit/) 
- [du](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/du.html)
- [tr](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/tr.html) 
- [dd](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/dd.html) 
- [od](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/od.html)
- [tail](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/tail.html) 
- [openssl](https://en.wikipedia.org/wiki/OpenSSL) 

#### MUST READ

[PRO GIT!](https://git-scm.com/book/en/v2)