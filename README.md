# bash
bash notes

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
may need to put in quotes to stop wildcard expansion in shell such as zsh
different shell behaves differently for wildcard expansion by shell before sending it to the command
eg.
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
greg -RL "h" .
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