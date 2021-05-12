(Testing on Ubuntu 20.04 with file stored on an SSD.)

Count the total number of files (for reference):

```shell
tsbertalan@sigurd:~$ cd ~/Dropbox/
tsbertalan@sigurd:~/Dropbox$ find . | wc
 513446 1329333 44711578

```

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

Still pretty fast.