This is pair of simple scripts for doing local full-text search. Put them in ~/bin on linux (and probably mac too) and make them executable.

Search results show line numbers and a little context around the result. It's not particularly extensible in itself, but short enough you could easily rewrite it to do something else, like return results in a more machine-parseable form.

I'll test here with Ubuntu 20.04, and files stored on an SSD. However, after *find* does its caching, it's no slower on another 16.04 machine with spinning rust.

Count the total number of files (for reference):

```shell
tsbertalan@sigurd:~$ cd ~/Dropbox/
tsbertalan@sigurd:~/Dropbox$ find . | wc
 513446 1329333 44711578

```

Half a million files.

Count the number of markdown files (for example):

```shell
tsbertalan@sigurd:~/Dropbox$ find . -iname "*.md" | wc
   2081    9562  162239
```

**Actual usage:** a (timed) search.

```shell
tsbertalan@sigurd:~/Dropbox$ time ffg md "This is a search test."
Searching for " This is a search test. " in md files.



===============================================================================
./testfile.md
1:This is a search test.
2-

real	0m1.302s
user	0m0.673s
sys	0m0.643s
```

Let's try a search with slightly more results:

```shell
tsbertalan@sigurd:~/Dropbox$ echo "ffg md the | wc" > slower_search.sh
tsbertalan@sigurd:~/Dropbox$ time bash slower_search.sh 
  67364  749145 7937814

real	0m8.722s
user	0m2.167s
sys	0m1.862s
```

How many actual results was that?
```shell
tsbertalan@sigurd:~/Dropbox$ ffg md the | grep ============= | wc
   1252    1252  100185
```

(This is admittedly a pretty rough way to count them, but good enough for government work.)

Still pretty fast.

