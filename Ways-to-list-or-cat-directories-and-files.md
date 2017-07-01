# Ways to list or cat directories and files

## Introduction

To list a directroy, we can simply use `ls` command; To cat a file, we can simply use `cat` command.

But, if today we cannot use those commands, what else can we use?

### Tricks

- Mutated `ls` and `cat` - bash hacks

```bash
bash$ l``s
test  test_dir
bash$ c``at test
this is a test
1
2
3

```

- `du` - List all files:

```bash
bash$ du *
4	test
4	test_dir
```

- `tac` - Reversed version of `cat`

```bash
bash$ cat test
this is a test
1
2
3

bash$ tac test

3
2
1
this is a test
```

- `rev`: reverse line

```bash
bash$ cat test
this is a test
1
2
3

bash$ rev test
tset a si siht
1
2
3

```

- `strings` - print the strings of printable characters in files

```bash
bash$ strings test
this is a test
```

- `xxd`: make a hexdump or do the reverse

```bash
bash$ cat test
this is a test
1
2
3

bash$ xxd test
0000000: 7468 6973 2069 7320 6120 7465 7374 0a31  this is a test.1
0000010: 0a32 0a33 0a0a                           .2.3..
```

- `awk`

```bash
bash$ awk '{system("wc "$0)}' unknown
wc: very: No such file or directory
wc: secret: No such file or directory
0 0 0 total
```

### Extenal Program

- ftp - `!/bin/sh`
- gdb - `!/bin/sh`
- more / less / man - `!/bin/sh`
- vi / vim - `:!/bin/sh`
- scp `-S /tmp/getMeOut.sh` x y:
- awk `'BEGIN {system("/bin/sh")}'`
- find / -name someName `-exec /bin/sh` \;

## Impact

These sort of things often used to break out from the restricted shell environment.

For instance, [CVE-2017-8386](https://github.com/qazbnm456/awesome-cve-poc#cve-2017-8386) takes advantage of executing arbitrary commands from `less` command.

## Reference

1. https://github.com/ctfs/write-ups-2015/tree/master/volgactf-quals-2015/pwn/bash
2. [Escape From SHELLcatraz - Breaking Out of Restricted Unix Shells](https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells)
