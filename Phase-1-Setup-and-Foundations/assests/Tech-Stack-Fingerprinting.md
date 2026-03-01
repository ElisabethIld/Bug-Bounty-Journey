**Common Vulnerabilities and Exposures (CVEs)**

Gives each CVE a number for reference, database (https://cve.mitre.org/cve/search_cve_list.html).

`$ nmap scanme.nmap.org -sV`

Next, in *Burp*, send an **HTTP request** to the server: Send to Repeater -> Send (then see Response):

```
Server: Apache/2.0.6 (Ubuntu)
X-Powered-By: PHP/5.0.1
X-Generator: Drupal 8
X-Drupal-Dynamic-Cache: UNCACHEABLE
Set-Cookie: PHPSESSID=abcde;
```
Cookie named `PHPSESSID`, it’s probably developed using `PHP`.

Right-click a page, select *View Source Code*, and press **CTRL-F** to search for *phrases* like `powered by`, `built with`, and `running`. 
For instance, you might find `Powered by: WordPress 3.3.2` written in the source.

`phpmyadmin` at the root directory, like `https://example.com/phpmyadmin`, means the application runs `PHP`.

A directory named `jinja2` that contains templates, probably uses `Django` and `Jinja2`.

- **Wappalyzer** (https://www.wappalyzer.com/) a browser extension,
- **BuiltWith** (https://builtwith.com/) a website that shows you which web technologiesa site is built with,
- **StackShare** (https://stackshare.io/) is an online platform that allows developers to share the tech they use,
- **Retire.js** is a tool that detects *outdated* `JavaScript` libraries and `Node.js` packages.

