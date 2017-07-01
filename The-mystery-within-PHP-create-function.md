# The mystery within PHP create_function()

> This function has been DEPRECATED as of PHP 7.2.0. Relying on this function is highly discouraged.

## Introduction

Its official description: Creates an anonymous function from the parameters passed, and returns a unique name for it.

That is, it creates an anonymous function for you when you needed, which is a very useful API call especially when you need something like dynamically generated functions.

However, thare're some trivia they don't tell you.

Firstly, do you know what create_function() actually does, is creating an ordinary function with name chr(0).lambda_n where n is some number?

For instance:

```php
$a = create_function('$a', 'return $a;');
echo $a(123);             // 123

echo "\x00lambda_1"(123); // ???
```

Secondly, do you notice there's warning in manual says that this function internally performs an eval() and as such has the same security issues as eval().

Therefore, some CVE were brought due to misusing this function!

## Impact

Let's take a WordPress RCE case which is taken from https://gist.github.com/anonymous/908a087b95035d9fc9ca46cef4984e97.

The vulnerability code in wp-includes/pomo/translations.php:

```php
function make_plural_form_function($nplurals, $expression) {
    $expression = str_replace('n', '$n', $expression);
    $func_body = "
        \$index = (int)($expression);
        return (\$index < $nplurals)? \$index : $nplurals - 1;";
    return create_function('$n', $func_body);
}
```

Since the manual has said that create_function() function internally performs an eval(), so an attacker can use `}` to close the anonymous function and execute arbitrary code whatever they want.

## Reference

1. http://php.net/manual/en/function.create-function.php
2. https://gist.github.com/anonymous/908a087b95035d9fc9ca46cef4984e97
3. [WordPress <= 4.6.1 使用语言文件任意代码执行 漏洞分析](http://blog.knownsec.com/2016/10/wordpress-4-6-1-language-exploit/)
