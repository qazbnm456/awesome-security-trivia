# MySQL madness with exponent

## Introduction

When an exponent literal appears in the query string, a weird syntax seems to be acceptable to MySQL engine.

> This trivia was discovered in Hack.lu CTF 2015 web challenge `Grading Board`, and its source code and official writeup can be found at [jhector/grading-board](https://github.com/jhector/grading-board).

This challenge should have prevented `union` keyword in subquery that users can control according to the author.

The preventing mechanism is at [jhector/grading-board#include/Database.php](https://github.com/jhector/grading-board/blob/master/include/Database.php#L23), and the most important part of the code is:

```php
if (preg_match('/[^a-zA-Z0-9_]union[^a-zA-Z0-9_]/i', $condition))
    throw new Exception('oO come on, stop being silly...');
```

Thus, passing something like `or 1=2 union select 1,2,3#\` will trigger the system to throw out the error message.

Nevertheless, one can bypass the limitation by passing `or 1=6e0union select 1,2,3 #\`. There is no need to give an empty space between `6e0` and `union`!

## Impact

![rHdHAjU.png](http://i.imgur.com/rHdHAjU.png)

## Reference

1. [jhector/grading-board](https://github.com/jhector/grading-board)
