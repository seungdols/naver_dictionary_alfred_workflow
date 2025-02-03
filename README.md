# alfred workflow - naver 사전

ref: https://seungdols.tistory.com/1018

원래 이 워크플로우는 초기에 내가 만든게 아니라, 다른 분이 만든 코드를 계속 변경하였습니다.

기존 코드 베이스가 python2 였는데, python3 코드로 바꾸면서, 계속 뭔가 동작을 하지 않길래, 보니까 

네이버 영어사전 쪽 자동완성 API 주소가 바뀌었더라... 그래서 다시 수정 했습니다.

## source 

```python
# -*- coding:utf-8 -*-

import urllib.parse
import urllib.request
import json
import unicodedata

q = "{query}"  # 검색어를 여기에 넣습니다.
q2 = unicodedata.normalize('NFC', q)
q3 = urllib.parse.quote(q2.encode('utf-8'))

url = f'https://ac-dict.naver.com/enko/ac?q={q3}&q_enc=utf-8&st=11&r_format=json&r_enc=utf-8&r_lt=11&r_unicode=0&r_escape=1'

with urllib.request.urlopen(url) as response:
    unparsed = response.read()

obj = json.loads(unparsed)

elems = []
i = 0

if 'items' in obj and obj['items'][0]:
    for item_group in obj['items']:
        for item in item_group:
            if len(item) < 3:
                continue

            en = item[0][0]
            ko = item[2][0]
            ko = ko.replace('<b>', '').replace('</b>', '')

            word = f'{en}: {ko}'

            s = f'<item uid="{en}_{i}" arg="{en}"><title>{word}</title><subtitle>네이버 영어 사전에서 &quot;{en}&quot; 검색</subtitle><icon>icon.png</icon></item>'
            elems.append(s)
            i += 1

if len(elems) == 0:
    s = f'<item uid="{q3}_0" arg="{q3}"><title>{q3}</title><subtitle>네이버 영어 사전에서 &quot;{q3}&quot; 검색</subtitle><icon>icon.png</icon></item>'
    elems.append(s)

print("<items>")
for elem in elems:
    print(elem)
print("</items>")

```
