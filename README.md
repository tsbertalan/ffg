This is pair of simple scripts for doing local full-text search among files of a given extension. Put them in ~/bin on linux (and probably mac too) and make them executable.

The search probably isn't best-practice (with a database of  some sort of string prefices), but it's so fast for my common use of many small text/code files, and so simple to write and read, that I don't care.

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

**Actual usage here** (remove the `time` at the beginning for normal, non-timed usage):

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

Let's do a clumsy search for function definitions in Python files:

```shell
tsbertalan@sigurd:~/Dropbox$ find . -iname "*.py" | wc
   8024   15736  624813
tsbertalan@sigurd:~/Dropbox$ echo "ffg py def | wc" > slower_search.sh
tsbertalan@sigurd:~/Dropbox$ time bash slower_search.sh 
 334322  999241 18839714

real	0m9.305s
user	0m6.502s
sys	0m2.617s
tsbertalan@sigurd:~/Dropbox$ ffg py def | grep ============= | wc
   5677    5721  454105
```

Hmm, only 5677 results among 8024 files, for a total of 334322 lines of search output. 

...

After some testing, though I'm not really a C++ programmer, I find I somehow have more than 8000 cpp files in my Dropbox (mostly accounted for by some Arduino libraries, and redundant [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) and [Ipopt](https://coin-or.github.io/Ipopt/) source trees). Let's search them for opening curly brackets.

```shell
tsbertalan@sigurd:~/Dropbox$ find . -iname "*.cpp" | wc
   8191   11799  758901
tsbertalan@sigurd:~/Dropbox$ echo "ffg cpp \"{\" | wc" > slower_search.sh
tsbertalan@sigurd:~/Dropbox$ cat slower_search.sh 
ffg cpp "{" | wc
tsbertalan@sigurd:~/Dropbox$ time bash slower_search.sh 
 519909 1767949 31093177

real	0m7.256s
user	0m6.075s
sys	0m2.098s
tsbertalan@sigurd:~/Dropbox$ ffg cpp "{" | grep ============= | wc
   5984    5999  478888
```

Still pretty fast. Times here are similar (12 seconds) if you instead search for the letter "e", **since it really scales mostly with the number of files with the given extension it finds, not the number of query occurrences per file.** 

