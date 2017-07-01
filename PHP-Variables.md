# PHP Variables

## Introduction

PHP will convert all dots and spaces in variable names to underscores, and this is the standard PHP behaviour.

In the [official manual](http://php.net/manual/en/language.variables.external.php), it says:

> Note:
> Dots and spaces in variable names are converted to underscores. For example \<input name="a.b" /> becomes $_REQUEST["a_b"].

## Impact

Since this behaviour isn't that well-known to developers, it's obviously some developers will write some validating functions like:

```php
if (strstr($_SERVER['QUERY_STRING'], '_')) {
    die("'_' is not allowed!")
} else {
    do_something();
}
```

The developer may want to ensure that users cannot give any query key containing underscore, because variable names with underscore often refer to private or sensitive variables.

However, we know PHP will convert dots and spaces in variable names to underscores now, it's easy to bypass the limitation.

## Reference

1. https://github.com/php/php-src/blob/master/main/php_variables.c#L93
2. [Variables From External Sources](http://php.net/manual/en/language.variables.external.php)
