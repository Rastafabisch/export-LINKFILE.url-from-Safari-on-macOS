# export-universal-.URL-link-file-from-Safari-on-macOS-
macOS service allowing the creation of .url links from safari

- - - - - - -

I created a small a macOS Safari service. What it does is taking the current tabs url, domain and name and saving it to a `$name ($domain).url`. 

It features the logic not to overwrite existing files, if the resulting name is the same, but the included url would be different. (It adds a number [starting from 2] to the file instead.)

As you can assign custom shortcuts to services I choose `ctrl s`.

This can be easily adopted to other macOS browsers if they feature Automator support, but would require additional logic, if they don't. Feel free to tinker around. 

As of now ([presumably] macOS 10.4 to macOS 11.2.3) the service works fine.

The "magic" is done by executing a small shell script after getting the current tabs URL using a Automator script.
```
#!/bin/bash
url=$@
tab=$(osascript -e 'tell application "Safari" to return name of front document' | tr -s '/' '|' | cut -d':' -f2- | awk '{$1=$1};1' )
name=~/Downloads/$tab" ("$(echo $url  | awk -F/ '{print $3}' | sed 's/www*\.//' )")"
ext=.url
if [[ -f "$name$ext" && ! $(grep -Fx "URL="$url $name$ext) ]] ; then
    i=2
    while [[ -f "$name-$i$ext" && ! $(grep -Fx "URL="$url $name-$i$ext) ]] ; do
   		let i++
    done
    name=$name-$i
fi
file=$name$ext
echo '[InternetShortcut]' > "$file"
echo -n 'URL=' >> "$file"
echo $url >> "$file"
#echo 'IconIndex=0' >> "$file"
```
