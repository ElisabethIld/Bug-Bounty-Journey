***Don’t recommend Google dorking on a corporate or shared network.***

- **site** 
to search for Python’s *print()* function - `print site:python.org`

- **inurl**
the existence of a page called
*/course/jumpto.php* on a website could indicate that it’s vulnerable - `inurl:"/course/jumpto.php" site:example.com`

- **intitle** - `intitle:"index of" site:example.com`

- **link** researching the uncommon *regular expression denial-of-service (ReDoS)* vulnerability - `link:"https://en.wikipedia.org/wiki/ReDoS"`

- **filetype** searches for pages with a specific file extension, the *.log* file extension - `filetype:log site:example.com`

- **Wildcard (*)** string that starts with *how to hack* and ends with *using Google* - `"how to hack * using Google"`

- **Quotes (" ")** exact match - *phrase* how to hack: `"how to hack"`

- **Or (|)** to search for one search term or the other, or both at the same time. Must be surrounded by spaces - `"how to hack" site:(reddit.com | stackoverflow.com)`

- **Minus (-)** excludes certain search results, *how to hack websites* but not *php* - `"how to hack websites" -php`

*Google search operators*

all of a company’s subdomains - `site:*.example.com`

Special endpoints that can lead to vulnerabilities. *Kibana* is a data visualization tool.
Many *Kibana* dashboards run under the path *app/kibana* - `site:example.com inurl:app/kibana`

Find company resources hosted by a third party online (like *Amazon S3 buckets*) - `site:s3.amazonaws.com COMPANY_NAME`

Look for special extensions. Often used for script files: *.log, .php, .cfm, .asp, .jsp, .pl* - `site:example.com ext:php` or `site:example.com ext:log`

This query searches the site *example.com* for text files that contain *password* - `site:example.com ext:txt password`

**Amazon S3:**
```
site:s3.amazonaws.com COMPANY_NAME
site:amazonaws.com COMPANY_NAME

amazonaws s3 COMPANY_NAME
amazonaws bucket COMPANY_NAME
amazonaws COMPANY_NAME
s3 COMPANY_NAME
```
