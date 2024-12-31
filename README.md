# bash
bash notes

use shellcheck to help...

file system commands
```
ls
pwd
cd
mount
find
grep
```


```
cat
echo $(< README)
history
```

find searches files better than cd or ls.
find will find hidden documents too
use find with glob pattern *?[]

```
may need to put in quotes to stop wildcard expansion in shell if a match is found by the shell expansion, it won't send the glob as arg to the command

find . -iname '*readme*'

find /
find . -iname readme
find . -name readme
find . -path */doc/bash/*
find . -type f
find . -type d
find . -name README -a -path */doc/bash/*
find . -name README -o -path */doc/bash/*
find . -not -name README
find . -maxdepth 1 -iname '*readme*'
find ~/all_files -type f -newermt 2019-11-01 ! -newermt 2019-12-01 -exec mv {} ~/files/2019/11 \;

for below, the curly bracket means insert the file names there
Execute the specified command on each found object
-exec command {} \;	
find -name README -type f -exec cp {} ∼ \;

Execute the specified command once over all objects found; the command receives all these objects as input	
-exec command {} +
find -type d -exec cp -t ∼ {} +

Delete each of the found files; the utility deletes empty directories only	
-delete	
find -name README -type f -delete

exec doesn't get bash features such as pipelines
```

grep, use it to find content of file, where as find finds the file
```
-n gives line number
grep -n "free software" /usr/share/doc/bash/README

use bash expansion to turn, it doesnt handle subdirectories
grep "free software" *
into
grep "free software" bash.txt xz.txt

recursively grep
grep -R "h" . 

use echo * to check how bash handles glob pattern expansion

find resurively no h
grep -RL "h" .
```

another way to find executable with type
```
find / -name find

vs

type find
type pwd
```

manual
```
man
help
```

file directory gnu utility
```
mkdir /tmp/doc
mkdir -p /tmp/doc/img
cp readme.txt tmp/doc/readme.txt
cp readme.txt tmp/doc
cp 
mv
rm
```

file system gnu utility
```
mkdir /tmp/docs
mkdir -p /tmp/docs

cp readme.txt tmp/readme.txt
cp -r /tmp ~/tmp

note if destination is a folder that doesn't exist, it will create it and put contents there. if the dest folder already exist, then it will put the source folder into the desk folder.

for more predictable cp,
use 
cp -r -t <dest> <sources>

mv ~/tmp ~/backup

rm doesn't take pattern, but can shell expand before hand

rm readme.txt
rm -rf ~/tmp
```

shell expand tries before sending result to the next command

unix stores permission of files in file object called bitmask
```
ls -l
```

required permissions
```
ls	r--	Reading	Applied for directories only.
cd	--x	Executing	Applied for directories only.
mkdir	-wx	Writing and executing	Applied for directories only.
rm	-w-	Writing	Specify the -r option for the directories.
cp	r--	Reading	The target directory should have writing and executing permissions.
mv	r--	Reading	The target directory should have writing and executing permissions.

Execution	r-x	Reading and executing.	Applied for files only.
```

unix system directories
```
/bin	It stores executable files of system utilities.
/etc	It stores configuration files of applications and system utilities.
/lib	It stores libraries of system utilities.
/usr/bin	It stores executable files of user applications.
/usr/lib	It stores libraries of user applications.
/usr/local	It stores applications that the user compiled on his own.
/usr/share	It stores architecture-independent resource files of user applications (e.g. icons).
/var	It stores files created by applications and utilities while running (e.g. log files).
```

Installing apps
```
sudo apt-get update
sudo apt-get install -y git
```

Adding diff folder to path variable
```
cd ~
vim ~/.profile

add to the end of the file
PATH="/opt/git/bin:${PATH}"
```

Best practice for software development in unix
```
Write programs that do one thing and do it well.
Write programs to work together.
Write programs to handle text streams, because that is a universal interface.
```

Redirection
```
0< 1> 2> 

same as

< > 2>

&> or 
> target 2>&1
find / -path */doc/* -name README > result_and_errors.txt 2>&1


0	Standard input stream (stdin).	A program receives input data from this stream. By default, it comes from an input device like a keyboard.
1	Standard output stream (stdout).	A program outputs data there. The terminal window prints this stream by default.
2	Standard error stream (stderr).	A program outputs the error messages there. The terminal window prints this stream by default.
```

```
2>&1 error stream points to the same target as the output stream.
```

Pipeline
pipes output from source to input of the other
```
grep -R "GNU" /usr/share/doc/bash

same as

info bash | grep -n "GNU"
```

```
du /usr/share -a -h | grep "\.html" | sort -h -r > result.txt

tee will duplicate the result to both stdout and the file6
du /usr/share -a -h | grep "\.html" | sort -h -r | tee result.txt
```

xargs
```
The xargs utility constructs a command. 
Utility takes two things as the input: parameters and text data from the input stream. 
Parameters come in the first place in the constructed command. 
All data from the input stream follows.

eg. puts output of find as input 
find ~ -type f | grep "bash"

eg. puts output of find as arguments which allows grep to execute as to find contents of each of the files listed
find ~ -type f | xargs grep "bash"

Apply the -I option to specify the place to insert parameters by the percent sign %
find /usr/share/doc/ -type f -name "*.html" | xargs -I % cp % ~

can also use any string other than % sign
find /usr/share/doc/ -type f -name "*.html" | xargs -I FILE cp FILE ~

t option of xargs displays the constructed commands before executing them. Use it for checking the utility’s results and debugging
find /usr/share/doc/ -type f -name "*.html" | xargs -t -I % cp % ~

Combine find and pipe with -P for parallel compute
eg. 4 parallel to utilize more cpu cores
find /usr/share/doc/ -type f -name "*.html" | xargs -P 4 -I % cp % ~

use find -print0, xargs -0, grep -Z options etc, to replace separators like linebreaks and spaces with NULL to allow handling of separators and line spaces to prevent splitting when not desired

Here are some general hints for using pipelines:
Be aware of spaces and line breaks when passing file and directory names via the pipeline.
Never process the ls output. Use the find utility with the -exec action instead.
Always use -0 option when processing the object names by xargs. Pass only null character separated names to the utility.
```

common utilities that can take input streams
```
xargs
grep
tee
sort
wc
head
tail
less
```

Using logical operator && and || by leveraging side effects of commands in bash

Logical operator &&
```
eg. copies the files, and only if cp utility returns 0 exit code (instead of any 1-255), put OK in result log.

cp -R ~/docs ~/docs-backup && echo "OK" > result.log

Bash stores an exit code of the last executed command in the environment variable. The variable name is the question mark. We can print its value with the echo command this way:

echo $?
```

Logical operator || 
```
cp -R ~/docs ~/docs-backup && echo "OK" > result.log || echo "Error" > result.log

explaination
A && B || C

if A is true, then B gets executed. if A && B are both true, then C won't need to get evaluated so its not executed

if A is false, then C will have to get evaluated so C is executed.
```

; semi colon
```
eg. executes next regardless of exit value of previous
cp -R ~/docs ~/docs-backup ; cp -R ~/photo ~/photo-backup

can do it with pipeline too, only if input stream of second one doesn't matter based on output of prev
cp -R ~/docs ~/docs-backup | cp -R ~/photo ~/photo-backup

The - option of grep appends data from the input stream to the utility’s parameters.
ls /usr/share/doc/bash | grep "README" * -
```

shebang
```
this works on most linux, but not all
#!/bin/bash

use the env utility to search for  the path of the Bash executable in the list of the PATH variable.
#!/usr/bin/env bash
```

subshell
```
example:
() spawns subshell, the exit 1 only exits the subshell below, so it doesn't actually exit the parent shell

tar -cjf ~/photo.tar.bz2 ~/photo &&
  echo "tar - OK" > results.txt ||
  (echo "tar - FAILS" > results.txt ; exit 1)

cp -f ~/photo.tar.bz2 ~/backup &&
  echo "cp - OK" >> results.txt ||
  ! echo "cp - FAILS" >> results.txt


use { } to execute in current shell, without spawning subshell. remember the mandatory ;
tar -cjf ~/photo.tar.bz2 ~/photo &&
  echo "tar - OK" > results.txt ||
  { echo "tar - FAILS" > results.txt ; exit 1 ; }
```

set -e instead of exit 1. to terminate the script after first failed command
```
in bash
set -e

or inline
bash -e 

set -e only affects current shell, not subshell.
Bash executes each command of a pipeline or (some? not sure if && and || counts, when testing they didn't maybe its for logical operations not operators)logical operator in a separate subshell.

# Case #1
command1         # NO subshell
var=$(command1)  # 1 subshell (command substitution)

# Case #2
command1 | command2         # 2 subshells (1 for each pipeline segment)
var=$(command1 | command2)  # 3 subshells: + 1 for command subst.

# Case #3
command1 | command2 ; var=$?         # 2 subshells (due to the pipeline)
var=$(command1 | command2 ; echo $?) # 3 subshells: + 1 for command subst.;
                                     #   note that the extra command doesn't add 
                                     #   one
```

script parameters
```
$0 stores the path to the launched script
$1 is first param
$2 is second etc..

Bash stores all script parameters in ＄@
Bash has an alternative variable for ＄@. It is called ＄* Bash interprets its value as a single word. It interprets the ＄@ variable as a set of words in the same case.
make-backup.sh "one two three"
$*              vs $@
"one two three" vs "one" "two" "three"
We should always use ＄@ instead of ＄*. The only exception is when a single word must represent all input parameters.

use quote when sending paramter into bash.
once $1 is in bash, any subsequent calls will need quotes again because Shell performs quote removal before calling the function
```