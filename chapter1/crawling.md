# 크롤링\(Crawling\)과 스크레이핑(Scraping)
### 크롤링
웹 페이지의 하이퍼링크를 순회하면서 웹 페이지를 다운로드하는 작업
### 스크레이핑
다운로드한 웹 페이지에서 필요한 정보를 추출하는 작업

## Wget으로 크롤링하기
```
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
```
# full_book_list.html 내려받기
$ wget http://www.hanbit.co.kr/store/books/full_book_list.html

#원하는 데이터 추출
$ cat full_book_list.html | grep '<a href="/store/books/look.php'

#줄에서 원하는 문자열 위치 추출하기
$ echo '<td class="left"><a href="/store/books/look.php?p_code=B3448548347">'
```

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

```
$ give me super-powers
```

{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```
{% endcode %}



