---

title: "Information disclosure vuln. + 404 files access + Wayback mashine"

---

<h3><center>
  <strong>
    <span style="color:red">Information disclosure vuln. + 404 files access + Wayback mashine </span>
  </strong>
</center></h3>
<br>

---

<br>
<strong><span style="color:red">Crawl through Wayback Machine CDX API endpoints + from all possible subdomains: </span><strong><br>

From the CLI:
```bash
curl -G "https://web.archive.org/cdx/search/cdx" --data-urlencode "url=*.example.com/*" --data-urlencode "collapse=urlkey" --data-urlencode "output=text" --data-urlencode "fl=original" > out.txt
```

<span style="color:red">NOTE</span>: You will have a clear domains that may show 404 error. / Change *.example.com

<br>
From the browser bar:
```bash
https://web.archive.org/cdx/search/cdx?url=*.example.com/*&collapse=urlkey&output=text&fl=original
```

<br>
```bash
curl -G "https://web.archive.org/cdx/search/cdx" --data-urlencode "url=*.example.com/*" --data-urlencode "collapse=urlkey" --data-urlencode "output=text" --data-urlencode "fl=original" | awk '{print "https://web.archive.org/web/"$1"/"$2}' | tee waback_example.txt
```

<span style="color:red">NOTE</span>: Here u will have urls which going directly to the wayback mashine. / Change *.example.com as you prefere

---


<br>
<br>
<strong><span style="color:red">Advanced crawl with filtering for different file extentions + from all possible subdomains: </span></strong>
<br>

From the CLI:
```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.example.com/*&collapse=urlkey&output=text&fl=original&filter=original:.*\.(xls|xml|xlsx|json|pdf|sqldoc|docx|pptx|txt|git|zip|tar\.gz|tgz|bak|7z|rar|log|cache|secret|db|backup|yml|gz|config|csv|yaml|md|md5|exe|dll|bin|ini|bat|sh|tar|deb|rpm|iso|img|env|apk|msi|dmg|tmp|crt|pem|key|pub|asc)$" | tee output.txt
```

<span style="color:red">NOTE</span>: access the files with the direct link, if you have 404 error -> use Wayback machine to invistigate your findings, or use prepered command below which will give you links directly from Wayback mashine:

<br>
```bash
curl "https://web.archive.org/cdx/search/cdx?url=*.example.com/*&collapse=urlkey&output=text&fl=original&filter=original:.*\.(xls|xml|xlsx|json|pdf|sqldoc|docx|pptx|txt|git|zip|tar\.gz|tgz|bak|7z|rar|log|cache|secret|db|backup|yml|gz|config|csv|yaml|md|md5|exe|dll|bin|ini|bat|sh|tar|deb|rpm|iso|img|env|apk|msi|dmg|tmp|crt|pem|key|pub|asc)$" | awk '{print "https://web.archive.org/web/"$1"/"$2}' | tee waback_target_files.txt
```

<br>
<span style="color:=red">NOTE</span>: You will have domains directly from web archive! / Change *.example.com as you prefere.

---


<br>
<br>
<strong><span style="color:red">Search for confidantial information in PDF:</span></strong>

```bash
grep -Ei '\.pdf' output.txt | while read -r url; do curl -fsL "$url" | pdftotext - - 2>/dev/null | grep -Eaiq '(internal use only|confidential|strictly private|personal & confidential|private|restricted|internal|not for distribution|do not share|proprietary|trade secret|classified|sensitive|bank statement|invoice|salary|contract agreement|non disclosure|passport|social security|ssn|date of birth|credit card|identity|id number|company confidential|staff only|management only|internal only)' && echo "$url" done | tee output.txt
```

---


<br>
<br>
<strong><span style="color:red">You can find different endpoints, subdomains and etc etc: </span></strong>

```bash
https://www.virustotal.com/vtapi/v2/domain/report?apikey=your_api_key_here&domain=example.com
```
```bash
https://otx.alienvault.com/api/v1/indicators/hostname/example.com/url_list?limit=500&page=1 
```

---
