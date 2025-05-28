#linux 

**Back to [[Linux]]**

Filter output for specific word using **grep**.
```shell
cat file.txt | grep "word"
```

Eliminate duplicate lines from output using **uniq**.
```shell
cat file.txt | uniq
```

Eliminate duplicates from output and count occurrences of each value.
```shell
cat file.txt | uniq -c
```

Count number of lines with **wc**.
```shell
cat file.txt | wc -l
```

Show line numbers with **nl**.
```shell
cat file.txt | nl
```

**Also see:**
**[[Sorting]]**
