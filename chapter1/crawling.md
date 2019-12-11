# 크롤링\(Crawling\)과 스크레이핑(Scraping)
### 크롤링
웹 페이지의 하이퍼링크를 순회하면서 웹 페이지를 다운로드하는 작업
### 스크레이핑
다운로드한 웹 페이지에서 필요한 정보를 추출하는 작업

## Wget으로 크롤링하기
```bash
$ wget -r --no-parent -w 1 -l 1 --restrict-file-name=nocontrol http://www.hanbit.co.kr/
```
옵션 | 설명
---- | ---- 
-r, --recursive | 링크를 돌며 재귀적으로 다운로드한다
-np, --no-parent | 재귀적으로 다운로드할 때 부모 디렉토리는 크롤링하지 않는다
-w \<second\>, --wait=\<seconds\> | 재귀적으로 다운로드할 때 다운로드 간격을 seconds 초로 지정한다
-l \<list\>, --include \<list\> | 재귀적으로 다운로드할 때 list에 포함돼 있는 디렉토리만 돈다

### 명령어 의미
-l 옵션으로 링크를 한 번만 더 타고 들어가게 만들고, -w 옵션으로 다운로드 간격을 1초로 지정해 서버의 부하를 줄인다.
이어서 -no-parent 옵션으로 부모 디렉터리를 크롤링하지 않게 만들며, --resttrict-file-names=nocontrol로 URL에 한국어 등이 포함돼 있을 경우 한국어 파일명으로 저장한다.

### 실행 결과
www.hanbit.co.kr/ 이라는 디렉터리가 만들어지며, 내부에 파일이 다운로드된다.
실행 결과는 트리 구조의 디렉터리로 구성된다.

## 유닉스 명렁어로 스크레이핑하기
```bash
# full_book_list.html 내려받기
$ wget http://www.hanbit.co.kr/store/books/full_book_list.html

# 원하는 데이터 추출
$ cat full_book_list.html | grep '<a href="/store/books/look.php'

# 줄에서 원하는 문자열 위치 추출하기
$ cat full_book_list.html | grep '<a href="/store/books/look.php?p_code=' | sed -E 's/<[^>]*>//g'

# 앞 쪽 공백 제거
$ cat full_book_list.html | grep '<a href="/store/books/look.php?p_code=' | sed -E 's/<[^>]*>//g' | sed -E 's/^\s*//'

# &#40; &#41; ()로 치환
$ cat full_book_list.html | grep '<a href="/store/books/look.php?p_code=' | sed -E 's/<[^>]*>//g' | sed -E 's/^\s*//' | sed -E 's/&#40;/\(/g' | sed -E 's/&#41;/\)/g'
```
명령어 | 설명
---- | ----
sed | 특정 조건에 맞는 줄을 치환하거나 제거한다 's/\<정규표현식\>/\<치환할문자\>/\<옵션\>'

## 정리
유닉스 명령어만으로도 간단하게 크롤링과 스크레이핑을 할 수 있다.
이러한 명령어는 가볍지만 실용적인 기능이 부족하다. wget은 디렉터리 단위의 제한밖에 걸지 못하며, 순서를 명시적으로 지정해서 링크를 따라 들어가는 등의 제어도 할 수 없다. 또한 파일을 다운로드하는 시점에 다른 처리도 불가능하다. 스크레이핑 때도 마찬가지의 문제를 가진다. 유닉스 명령어는 한 줄을 처리하는 데 특화되어 있으므로 여러 줄의 데이터 처리가 힘들다.

# 파이썬으로 시작하는 크롤링/스크레이핑
## 파이썬을 사용할 때의 장점
읽기 쉽다.  
풍부한 서드파티 라이브러리를 가지고 있다.  
강력한 데이터 분석 라이브러리가 있어 스크레이핑 이후 처리에 용이하다.  

## 웹 페이지에서 데이터 추출하기
### 정규 표현식
HTML을 단순한 문자열로 취급하고, 필요한 부분을 추출한다. 절대로 마크업되지 않은 웹 페이지도 문자열의 특징을 파악하면 쉽게 스크레이핑할 수 있다.

```
import re
from html import unescape

# 파일을 열고 html이라는 변수에 저장한다.
with open('dp.html') as f:
  html = f.read()

#re.findall()을 사용해 도서 하나에 해당하는 HTML 을 추출한다.
for partial_html in re.findall(r'<td class="left"<a.*?</td>', html, re.DOTALL):
  # 도서의 URL을 추출
  url = re.search(r'<a href="(.*?)"', partial_html).group(1)
  url = 'http://www.hanbit.co.kr' + url
  
  # 태그를 제거해서 도서의 제목을 추출한다.
  title = re.sub(r'<.*?>', '', partial_html)
  title = unescape(title)
  print('url:', url)
  print('title:', title)
  print('---')
```

### XML 파서
XML 태그를 분석(파싱)하고, 필요한 부분을 추출한다. 

```
# ElementTree 모듈을 로드한다.
from xml.etree import ElementTree

# parse() 함수로 파일을 읽어들이고 ElementTree 객체를 로드한다.
tree = ElementTree.parse('rss.xml')

# getroot() 메서드로 XML의 루트 요소를 추출한다.
root = tree.getroot()

# findall() 메서드로 요소 목록을 추출한다
# 태그를 찾는다.
for item in root.findall('channel/item/description/body/location/data')
  # find() 메서드로 요소를 찾고 text 속성으로 값을 추출한다.
  tm_ef = item.ifnd('tmEf').text
  tmn = item.find('tmn').text
  tmx = item.find('tmx').text
  wf = item.find('wf').text
  
  # 출력
  print(tm_ef, tmn, tmx, wf)
```
