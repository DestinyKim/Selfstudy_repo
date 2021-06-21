```python
import requests
```


```python
res = requests.get("http://google.com")
print("응답코드:",res.status_code) ## 200이면 정상
```

    응답코드: 200
    


```python
if res.status_code == requests.codes.ok :  ## 연결이 정상이면 ":정상입니다."
    print("정상입니다.")                   ## 연결에 문제가 있으면 "문제가 생겼습니다."
else:
    print("문제가 생겼습니다.[에러코드",res.status_code,"]")
```

    정상입니다.
    


```python
res.raise_for_status() # 올바르게 코드를 가지고 오면 괜찮지만, 에러가 나면 알려줌
print("웹 스크래핑을 진행합니다.")
```

    웹 스크래핑을 진행합니다.
    


```python
===================================================================
보통 requests.get()와 raise_for_status() 같이 사용하여 잘 연결됐는지 확인한다.
```


```python
res = requests.get("http://google.com")
res.raise_for_status()
```


```python
len(res.text) #Google 페이지 안의 글자 갯수
```




    15230




```python
### 파일로 만들어 보자.
```


```python
with open("mygoogle.html","w",encoding='utf8') as f:
    f.write(res.text)
```

### 정규식


```python
# 주민등록번호
# 901201-1111111

# 이메일주소
# nadocoding@gmail.com

# 차량번호
# 11가 1234 / 123가 1234

# IP 주소
# 192.168.0.1
```


```python
import re  # 정규식 라이브러리
```


```python
# abcd, book, desk
# ca?e
# care, cafe, case, cave
# caae, cabe, cace, cade, ...
```


```python
p = re.compile("ca.e") # . (ca.e) : 하나의 문자를 의미 
                       # ^ (de^) : 문자열의 시작  desk, detination (o) / fade(x)
                       # $ (se$) : 문자열의 끝 case, base(o) / face(x)
```


```python
m = p.match("case")
print(m.group()) # 매치되지 않으면 에러가 발생된다.
```

    case
    


```python
m = p.match("caffe")
print(m.group()) # 매치되지 않으면 에러가 발생된다.
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-21-b6179d0a1df2> in <module>
          1 m = p.match("caffe")
    ----> 2 print(m.group()) # 매치되지 않으면 에러가 발생된다.
    

    AttributeError: 'NoneType' object has no attribute 'group'



```python
def print_match(m):
    if m:
        print("m.group() :",m.group())  # 일치하는 문자열 반환
        print("m.string :",m.string)    # 입력받은 문자열
        print("m.start() :",m.start())  # 일치하는 문자열의 시작 index
        print("m.end() :", m.end())     # 일치하는 문자열의 끝 index
        print("m.span() :", m.span())   # 일치하는 문자열의 시작 / 끝 index
    else:
        print("매칭되지 않음")
        
m = p.match("cafe")
print_match(m)

m = p.match("good care")
print_match(m)

# m2 = p.match("careless")   # match() : 주어진 문자열의 처음부터 일치하는지 확인
# print_match(m2)
```

    m.group() : cafe
    m.string : cafe
    m.start() : 0
    m.end() : 4
    m.span() : (0, 4)
    매칭되지 않음
    


```python
m = p.search("good care") # search() : 주어진 문자열 중에 일치하는게 있는지 확인
print_match(m)
```

    m.group() : care
    m.string : good care
    m.start() : 5
    m.end() : 9
    m.span() : (5, 9)
    


```python
lst = p.findall("good care cafe")  # findall : 일치하는 모든 것들 리스트 형태로 반환
print(lst)
```

    ['care', 'cafe']
    


```python
# 1. re.compile("원하는 형태")
# 2. m = p.match("비교할 문자열") : 주어진 문자열의 처음부터 일치하는지 확인
# 3, m = p.search("비교할 문자열") : 주어진 문자열 중에 일치하는게 있는지 확인
# 4. lst = p.findall("비교할 문자열") : 일치하는 모든 것을 'List' 형태로 반환

# . (ca.e) : 하나의 문자를 의미 
# ^ (de^) : 문자열의 시작  desk, detination (o) / fade(x)
# $ (se$) : 문자열의 끝 case, base(o) / face(x)

# 참고자료) https://www.w3schools.com/python/python_regex.asp
```

### User Agent


```python
import requests

url = "http://nadocoding.tistory.com"
headers = {"User-Agent" : "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.106 Safari/537.36"}
## headers를 넣어줌으로써 자세한 정보들을 더 얻을 수 있다.

res = requests.get(url, headers = headers)
res.raise_for_status()
```


```python
with open("nadocoding.html", "w", encoding="utf8") as f:
    f.write(res.text)
```

=============================================================

### BeautifulSoup


```python
import requests
from bs4 import BeautifulSoup

url = "https://comic.naver.com/index.nhn"
res = requests.get(url)
res.raise_for_status()

soup = BeautifulSoup(res.text, "lxml")
soup.title
soup.title.get_text()
```




    '네이버 만화'




```python
soup.a   # soup 객체에서 처음 발견되는 a element 내용을 출력해줌
```




    <a href="#menu" onclick="document.getElementById('menu').tabIndex=-1;document.getElementById('menu').focus();return false;"><span>메인 메뉴로 바로가기</span></a>




```python
soup.a.attrs # a element의 속성 정보를 출력해줌
```




    {'href': '#menu',
     'onclick': "document.getElementById('menu').tabIndex=-1;document.getElementById('menu').focus();return false;"}




```python
soup.a["href"] # a element의 href 속성 값을 출력해줌
```




    '#menu'




```python
soup.find("a", attrs={"class":"Nbtn_upload"}) # class='Nbtn_upload'인 a element를 찾아줘
```




    '웹툰 올리기'




```python
soup.find(attrs={"class":"Nbtn_upload"}) # class='Nbtn_upload'인 어떤 element를 찾아줘
```




    <a class="Nbtn_upload" href="/mypage/myActivity.nhn" onclick="nclk_v2(event,'olk.upload');">웹툰 올리기</a>




```python
soup.find("a", class_="Nbtn_upload").text
```




    '웹툰 올리기'



===============================================


```python
soup.find("li", attrs={"class":"rank01"})
```




    <li class="rank01">
    <a href="/webtoon/detail.nhn?titleId=758037&amp;no=34" onclick="nclk_v2(event,'rnk*p.cont','758037','1')" title="참교육-34화">참교육-34화</a>
    <span class="rankBox">
    <img alt="변동없음" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_no.gif" title="변동없음" width="7"/> 0
    						
    					
    				</span>
    </li>




```python
rank1 = soup.find("li", attrs={"class":"rank01"})
rank1.a
```




    <a href="/webtoon/detail.nhn?titleId=758037&amp;no=34" onclick="nclk_v2(event,'rnk*p.cont','758037','1')" title="참교육-34화">참교육-34화</a>




```python
rank1.a.get_text()
```




    '참교육-34화'




```python
rank1.next_sibling
```




    '\n'




```python
rank2  = rank1.next_sibling.next_sibling
rank2.a.get_text()
```




    '신의 탑-3부 74화'




```python
rank3  = rank2.next_sibling.next_sibling
rank3.a.get_text()
```




    '백수세끼-80화 주꾸미 볶음'




```python
rank2 = rank3.previous_sibling.previous_sibling
rank2.a.get_text()
```




    '신의 탑-3부 74화'




```python
rank1.parent
```




    <ol class="asideBoxRank" id="realTimeRankFavorite">
    <li class="rank01">
    <a href="/webtoon/detail.nhn?titleId=758037&amp;no=34" onclick="nclk_v2(event,'rnk*p.cont','758037','1')" title="참교육-34화">참교육-34화</a>
    <span class="rankBox">
    <img alt="변동없음" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_no.gif" title="변동없음" width="7"/> 0
    						
    					
    				</span>
    </li>
    <li class="rank02">
    <a href="/webtoon/detail.nhn?titleId=183559&amp;no=493" onclick="nclk_v2(event,'rnk*p.cont','183559','2')" title="신의 탑-3부 74화">신의 탑-3부 74화</a>
    <span class="rankBox">
    <img alt="변동없음" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_no.gif" title="변동없음" width="7"/> 0
    						
    					
    				</span>
    </li>
    <li class="rank03">
    <a href="/webtoon/detail.nhn?titleId=733074&amp;no=80" onclick="nclk_v2(event,'rnk*p.cont','733074','3')" title="백수세끼-80화 주꾸미 볶음">백수세끼-80화 주꾸미 볶음</a>
    <span class="rankBox">
    <img alt="변동없음" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_no.gif" title="변동없음" width="7"/> 0
    						
    					
    				</span>
    </li>
    <li class="rank04">
    <a href="/webtoon/detail.nhn?titleId=602910&amp;no=367" onclick="nclk_v2(event,'rnk*p.cont','602910','4')" title="윈드브레이커-3부 - 117화 불편한 자리">윈드브레이커-3부 - 117화 불편한 자리</a>
    <span class="rankBox">
    <img alt="변동없음" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_no.gif" title="변동없음" width="7"/> 0
    						
    					
    				</span>
    </li>
    <li class="rank05">
    <a href="/webtoon/detail.nhn?titleId=748105&amp;no=102" onclick="nclk_v2(event,'rnk*p.cont','748105','5')" title="독립일기-QnA">독립일기-QnA</a>
    <span class="rankBox">
    <img alt="순위상승" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_up.gif" title="순위상승" width="7"/>3
    						
    						
    					
    				</span>
    </li>
    <li class="rank06">
    <a href="/webtoon/detail.nhn?titleId=648419&amp;no=300" onclick="nclk_v2(event,'rnk*p.cont','648419','6')" title="뷰티풀 군바리-295화_이동희 3">뷰티풀 군바리-295화_이동희 3</a>
    <span class="rankBox">
    <img alt="순위하락" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_down.gif" title="순위하락" width="7"/>1
    						
    						
    						
    					
    				</span>
    </li>
    <li class="rank07">
    <a href="/webtoon/detail.nhn?titleId=769317&amp;no=12" onclick="nclk_v2(event,'rnk*p.cont','769317','7')" title="삼매경-12화">삼매경-12화</a>
    <span class="rankBox">
    <img alt="순위하락" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_down.gif" title="순위하락" width="7"/>1
    						
    						
    						
    					
    				</span>
    </li>
    <li class="rank08">
    <a href="/webtoon/detail.nhn?titleId=709731&amp;no=143" onclick="nclk_v2(event,'rnk*p.cont','709731','8')" title="유일무이 로맨스-143화">유일무이 로맨스-143화</a>
    <span class="rankBox">
    <img alt="순위하락" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_down.gif" title="순위하락" width="7"/>1
    						
    						
    						
    					
    				</span>
    </li>
    <li class="rank09">
    <a href="/webtoon/detail.nhn?titleId=736277&amp;no=84" onclick="nclk_v2(event,'rnk*p.cont','736277','9')" title="싸움독학-84화 : 감기가 오려나@">싸움독학-84화 : 감기가 오려나@</a>
    <span class="rankBox">
    <img alt="순위상승" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_up.gif" title="순위상승" width="7"/>1
    						
    						
    					
    				</span>
    </li>
    <li class="rank10">
    <a href="/webtoon/detail.nhn?titleId=730174&amp;no=103" onclick="nclk_v2(event,'rnk*p.cont','730174','10')" title="칼가는 소녀-103화">칼가는 소녀-103화</a>
    <span class="rankBox">
    <img alt="순위하락" height="10" src="https://ssl.pstatic.net/static/comic/images/migration/common/arrow_down.gif" title="순위하락" width="7"/>1
    						
    						
    						
    					
    				</span>
    </li>
    </ol>




```python
rank2 = rank1.find_next_sibling("li")
rank2.a.get_text()
```




    '신의 탑-3부 74화'




```python
rank3 = rank2.find_next_sibling("li")
rank3.a.get_text()
```




    '백수세끼-80화 주꾸미 볶음'




```python
rank2 = rank3.find_previous_sibling("li")
rank2.a.get_text()
```




    '신의 탑-3부 74화'




```python
rank1.find_next_siblings("li")
```




    bs4.element.ResultSet



=======================================================================


```python
webtoon = soup.find("a", text="참교육-34화")  # a태그 안에 "참교육-34화"라고 적힌 a태그 전체를 갖고와줘
print(webtoon)
```

    <a href="/webtoon/detail.nhn?titleId=758037&amp;no=34" onclick="nclk_v2(event,'rnk*p.cont','758037','1')" title="참교육-34화">참교육-34화</a>
    


```python
import requests
from bs4 import BeautifulSoup

url = "https://comic.naver.com/webtoon/weekday.nhn"
res = requests.get(url)
res.raise_for_status()

soup = BeautifulSoup(res.text, "lxml")

# 네이버 웹툰 전체 목록 가져오기
cartoons = soup.find_all("a", attrs={"class":"title"})
# class 속성이 title인 모든 "a" element를 출력
for cartoon in cartoons:
    print(cartoon.get_text())
```

    신의 탑
    참교육
    뷰티풀 군바리
    파이게임
    윈드브레이커
    소녀의 세계
    신입일기
    장씨세가 호위무사
    삼매경
    앵무살수
    만렙돌파
    백수세끼
    요리GO
    칼가는 소녀
    유일무이 로맨스
    히어로메이커
    리턴 투 플레이어
    오늘의 순정망화
    잔불의 기사
    결혼생활 그림일기
    황제와의 하룻밤
    최후의 금빛아이
    물어보는 사이
    장난감
    수영만화일기
    모스크바의 여명
    하루의 하루
    평범한 8반
    아는 여자애
    바로 보지 않는
    순정말고 순종
    사랑의 헌옷수거함
    꿈의 기업
    야생천사 보호구역
    브랜든
    착한건 돈이된다
    또다시, 계약 부부
    선배, 그 립스틱 바르지 마요
    침범
    왕따협상
    이중첩자
    마지막 지수
    원하는 건 너 하나
    살아간다
    라서드
    모락모락 왕세자님
    백호랑
    기사님을 지켜줘
    이탄국의 자청비
    개밥 먹는 남자
    드로잉 레시피
    중독연구소
    사막에 핀 달
    그림자 신부
    트리거
    헬로맨스
    오로지 오로라
    보살님이 캐리해!
    와이키키 뱀파이어
    아, 쫌 참으세요 영주님!
    여신강림
    바른연애 길잡이
    한림체육관
    엽총소년
    하루만 네가 되고 싶어
    용사가 돌아왔다
    랜덤채팅의 그녀!
    사신소년
    니나의 마법서랍
    신도림
    중증외상센터 : 골든 아워
    헬58
    삼국지톡
    몬스터
    호랑이 들어와요
    달콤살벌한 부부
    집이 없어
    오피스 누나 이야기
    원주민 공포만화
    윌유메리미
    위아더좀비
    하우스키퍼
    나는 어디에나 있다
    아이레
    오늘의 순정망화
    견우와 선녀
    플레이, 플리
    용왕님의 셰프가 되었습니다
    고등매직
    빌런투킬
    천마는 평범하게 살 수 없다
    성인초딩
    빅맨
    올가미
    악인
    은주의 방 2~3부
    태시트
    정년이
    3cm 헌터
    NG불가
    아이즈
    제로게임
    그녀석 정복기
    연우의 순정
    급식러너
    열녀박씨 계약결혼뎐
    숲속의 담
    프린스 메이커
    나의 플랏메이트
    대신 심부름을 해다오
    나타나주세요!
    오파츠
    빛빛빛
    자판귀
    조선홍보대행사 조대박
    안식의 밤
    완벽한 가족
    언메이크
    풋내기들
    찐:종합게임동아리
    하나in세인
    피로만땅
    인문학적 감수성
    헬퍼 2 : 킬베로스
    전지적 독자 시점
    복학왕
    모죠의 일지
    급식아빠
    튜토리얼 탑의 고인물
    캐슬
    격기3반
    남주의 첫날밤을 가져버렸다
    화산귀환
    세상은 돈과 권력
    여주실격!
    노곤하개
    일렉시드
    연놈
    나쁜사람
    고삼무쌍
    엔딩 후 서브남을 주웠다
    닥터앤닥터 육아일기
    하렘의 남자들
    원수를 사랑하라
    빌드업
    판타지 여동생!
    마른 가지에 바람처럼
    오징어도 사랑이 되나요?
    귀곡의 문
    각자의 디데이
    언덕 위의 제임스
    사랑과 평강의 온달!
    마녀와 용의 신혼일기
    방탈출
    사상최강
    구주
    아도나이
    새벽 두 시의 신데렐라
    로어 올림푸스
    이츠마인
    수요웹툰의 나강림
    나의 계절
    더 트웰브
    반귀
    럭키언럭키
    당신의 향수
    얼굴천재
    칼부림
    범이올시다!
    말하는대로
    속보입니다
    웰컴 온보드
    뱀은 꽃을 먹는가
    기억흔적
    스캔들
    해귀
    노선도
    신선비
    내 룸메이트는 마네킹
    스몰
    천도
    저승사자 출입금지
    수상한 비밀상담부
    시효완성
    독립일기
    더 복서
    연애혁명
    기기괴괴
    이두나!
    노답소녀
    나노마신
    정글쥬스
    화이트 블러드
    겟백
    하드캐리
    무사만리행
    던전 씹어먹는 아티팩트
    신비
    포식동물
    꽃만 키우는데 너무강함
    흑막 여주가 날 새엄마로 만들려고 해
    트롤트랩
    쿠베라
    오늘의 순정망화
    최강전설 강해효
    선의의 경쟁
    수영만화일기
    돌아온 여기사
    소년의 기록
    만물의 영장
    평범한 낙원
    별을 삼킨 너에게
    마계인섬
    시월드 판타지
    달의 요람
    그 개, 만두
    그 황제가 시곗바늘을 되돌린 사연
    자취방 신선들
    롤랑롤랑
    로그아웃
    온실 속 화초
    성스러운 아이돌
    안개무덤
    루커피쳐
    멸망X초이스
    불편한 관계
    집사레인저
    어차피 남편은!
    야만의 시대
    겟라이프
    소녀180
    불릿 6미리
    혼모노트
    유리와 유리와 유리
    미드나잇 체이서
    카루나
    모어 라이프
    밤하늘에 구름운
    바른탕진 프로젝트
    보물과 괴물의 도시
    헬프미
    외모지상주의
    갓 오브 하이스쿨
    데드퀸
    재혼 황후
    1초
    개를 낳았다
    죽지 않으려면
    삼국지톡
    낙향문사전
    여성전용헬스장 진달래짐
    세기말 풋사과 보습학원
    더 게이머
    그들이 사귀는 세상
    살人스타그램
    서울역 드루이드
    성경의 역사
    플레이어
    걸어서 30분
    상남자
    복학생 정순이
    구남친이 내게 반했다
    고래별
    로판 빙의 만화
    난약
    히어로 킬러
    엽사:요괴사냥꾼
    뫼신 사냥꾼
    환상의 용
    A.I. 닥터
    가슴털 로망스
    그 기사가 레이디로 사는 법
    주님, 악마가 되게 해주세요!
    팬시X팬시
    감자마을
    피와 나비
    태권보이
    거래
    버그: 스티그마
    닥터 프로스트 시즌 3~4
    후덜덜덜 남극전자
    사람의 조각
    악몽일기
    너의 미소가 함정
    방과후 선녀
    도무지 그애는
    아찔한 전남편
    빨리감기
    도깨비 고개
    썸내일
    몽홀
    트럼프
    꽃 피우는 남자
    구독금지
    찬란하지 않아도 괜찮아, 새벽
    백년게임
    구주의 시간
    진짜 정말 맹세코 좋아해
    합법해적 파르페
    히트포인트
    강림전기 개정기
    매지컬 메디컬
    호랑이형님
    프리드로우
    모죠의 일지
    취사병 전설이 되다
    광장
    니나의 마법서랍
    욕망일기
    노곤하개
    힙한남자
    토끼대왕
    좀비 파이트
    청춘 블라썸
    아홉수 우리들
    나이트런
    윌유메리미
    나를 바꿔줘
    어글리후드
    피라미드 게임
    동트는 로맨스
    반드시 해피엔딩
    나태 공자, 노력 천재 되다
    갓핑크
    지옥급식
    두번째 생일
    중매쟁이 아가 황녀님
    공유몽
    메트로헌터
    왕년엔 용사님
    단편.zip
    광해의 연인
    먹이
    웰캄투실버라이프
    남자주인공의 여자사람친구입니다
    태백 : 튜토리얼 맨
    내게 필요한 NO맨스
    군주
    왕세자 입학도
    저무는 해, 시린 눈
    키스 식스 센스
    2-3승강장
    율리
    동네몬스터
    압락사스
    팔려 온 신부
    남편을 만렙으로 키우려 합니다
    더 나우
    좋은데 어떡해
    모두 너였다
    오늘부터 천생연분
    아가사
    후아유!
    도사 가온
    손 잡아 볼래?
    안녕, 이바다씨
    아침을 지나 밤으로
    인간졸업
    언플래시레슨
    누군가의 로섬
    친구의 비밀계정
    주욱 같은 하루
    함부로 대해줘
    어른의 계절
    독립일기
    싸움독학
    이번 생도 잘 부탁해
    약한영웅
    열렙전사
    입학용병
    맘마미안
    소녀재판
    곱게 키웠더니, 짐승
    테러대부활
    닥터앤닥터 육아일기
    천하제일인
    오로지 너를 이기고 싶어
    살아남은 로맨스
    나만 보여!
    내일
    전설의 화석
    행성인간
    별이삼샵
    마법스크롤 상인 지오
    구름이 피워낸 꽃
    생존로그
    동생친구
    다이스(DICE)
    판사 이한영
    가짜인간
    벚꽃이 흩날릴 무렵
    로어 올림푸스
    제타
    AI가 세상을 지배한다면
    강남도깨비
    학교정벌
    합격시켜주세용
    라일락 200%
    사람은 고쳐 쓰는 게 아니야!
    호수의 인어
    평행도시
    아르세니아의 마법사
    결혼까지 망상했어!
    오른눈이 보는 세계
    굿 리스너
    데빌샷
    잉여특공대
    두근두근 연극부
    조선여우스캔들
    라커, 오프너
    오늘 밤만 재워줘
    마도
    소녀 해미
    위험한 신입사원
    푸른불꽃
    데이즈
    샤인 스타
    호시탐탐
    

====================================================================


```python
# 중증외상센터 : 골든 아워

import requests
from bs4 import BeautifulSoup

url = "https://comic.naver.com/webtoon/list.nhn?titleId=738174&weekday=tue"
res = requests.get(url)
res.raise_for_status()

soup = BeautifulSoup(res.text, "lxml")
cartoons = soup.find_all("tr")
len(cartoons[2:])


# cartoons[0].find("td",class_="title").a.text #제목

# cartoons[0].find("td",class_="title").a["href"] # 링크

# cartoons[0].find("div",class_="rating_type").strong.text #평점
```




    10




```python
# title = cartoons[1].a.get_text()
# link = cartoons[1].a["href"]
# print(title,":","https://comic.naver.com"+link)

titles = []
links = []
rates = []

for cartoon in cartoons[2:]:
    # 제목
    title = cartoon.find("td",class_="title").a.text
    titles.append(title) 
    
    # 링크
    link = "https://comic.naver.com"+cartoon.find("td",class_="title").a["href"]
    links.append(link)
    
    # 평점
    rate = cartoon.find("div",class_="rating_type").strong.text
    rates.append(rate)
    
    print(titles, links, rates) 
```

    ['2부 4화 : 뜯어고치려면_2'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue'] ['9.98']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue'] ['9.98', '9.98']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue'] ['9.98', '9.98', '9.97']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue'] ['9.98', '9.98', '9.97', '9.96']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기', '64화 : 정리(1부 완결)'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=64&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97', '9.97']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기', '64화 : 정리(1부 완결)', '63화 : 신의 영역 (2)'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=64&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=63&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97', '9.97', '9.98']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기', '64화 : 정리(1부 완결)', '63화 : 신의 영역 (2)', '62화 : 신의 영역 (1)'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=64&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=63&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=62&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97', '9.97', '9.98', '9.97']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기', '64화 : 정리(1부 완결)', '63화 : 신의 영역 (2)', '62화 : 신의 영역 (1)', '61화 : 기도 화상은 무섭다'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=64&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=63&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=62&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=61&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97', '9.97', '9.98', '9.97', '9.95']
    ['2부 4화 : 뜯어고치려면_2', '2부 3화 : 뜯어고치려면_1', '2부 2화 : 의술만 배우러 온 게 아니야', '2부 1화 : 뉴욕', '1부 후기', '64화 : 정리(1부 완결)', '63화 : 신의 영역 (2)', '62화 : 신의 영역 (1)', '61화 : 기도 화상은 무섭다', '60화 : 배움에는 끝이 없잖아'] ['https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=69&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=68&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=67&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=66&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=65&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=64&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=63&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=62&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=61&weekday=tue', 'https://comic.naver.com/webtoon/detail.nhn?titleId=738174&no=60&weekday=tue'] ['9.98', '9.98', '9.97', '9.96', '9.97', '9.97', '9.98', '9.97', '9.95', '9.98']
    


```python
import pandas as pd
from pandas import DataFrame

data = {"제목":titles, "평점":rates,"링크":links}
dat = pd.DataFrame(data)
dat
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>제목</th>
      <th>평점</th>
      <th>링크</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2부 4화 : 뜯어고치려면_2</td>
      <td>9.98</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2부 3화 : 뜯어고치려면_1</td>
      <td>9.98</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2부 2화 : 의술만 배우러 온 게 아니야</td>
      <td>9.97</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2부 1화 : 뉴욕</td>
      <td>9.96</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1부 후기</td>
      <td>9.97</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>64화 : 정리(1부 완결)</td>
      <td>9.97</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>63화 : 신의 영역 (2)</td>
      <td>9.98</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>62화 : 신의 영역 (1)</td>
      <td>9.97</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>61화 : 기도 화상은 무섭다</td>
      <td>9.95</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>60화 : 배움에는 끝이 없잖아</td>
      <td>9.98</td>
      <td>https://comic.naver.com/webtoon/detail.nhn?tit...</td>
    </tr>
  </tbody>
</table>
</div>


