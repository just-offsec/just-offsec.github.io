---

title: "Information disclosure vuln. + 404 files access + Wayback mashine"

---

<h3><center>
  <strong>
    <span style="color:red">Java-script files hunting. </span>
  </strong>
</center></h3>
<br>

---

search for js files from the wayback mashine from all possible subdomains:
>curl "https://web.archive.org/cdx/search/cdx?url=*.example.com/*&collapse=urlkey&output=text&fl=original&filter=original:.*\.(js|min\.js|bundle\.js|chunk\.js)$" | tee example_js.txt
NOTE: output will be: https://example.com/file.js


The same command but with modification:
>curl "https://web.archive.org/cdx/search/cdx?url=*.example.com/*&collapse=urlkey&output=text&fl=timestamp,original&filter=original:.*\.(js|min\.js|bundle\.js|chunk\.js)$" | awk '{print "https://web.archive.org/web/"$1"/"$2}' | tee wayback_js_urls.txt
NOTE: output will be: https://web.archive.org/web/https://example.com/file.js


>echo target.com | gau | grep '\.js$' | anew alljs.txt
NOTE: (gau(GetAllURLs) using wayback mashine and many more other webtools for crawling, "anew" adding to the old .txt file new lines without dublicates.)

>katana -u target.com -d 5 -jc | grep '\.js$' | tee alljs.txt
NOTE:  katana searches existing js files from current page.


---


---

Searching for Juicy info from gained .js using different tools:

Getting 200 responce:
>cat alljs.txt | uro | sort -u | httpx-toolkit -mc 200 -o target.txt

Using nuclei templates:
>cat js_file.txt | nuclei -t /home/user/nuclei-templates/credentials-disclosure-all.yaml -c 30



---
