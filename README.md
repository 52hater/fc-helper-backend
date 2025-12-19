# "깃허브는 잘 보이기 위한 곳이 아니다."


# fc-helper-backend
NEXON FC-Online helper web service
넥슨 FC 온라인 도우미


# 문서 개요

- 본 문서는 Nexon Open API(openapi.nexon.com)에서 제공하는 모든 API를 기반으로 작성되었습니다.
- 예시에 사용된 정보들은 실제 특정 사용자 데이터와는 무관합니다.
- 데이터는 매시 정각, 두 시간 전의 게임 데이터가 업데이트됩니다.
  ex) 오후 3시 5분에 API 호출 → 오늘 오후 1시까지의 정보 조회 가능
- 게임 콘텐츠 변경(점검 및 업데이트 / 로스터 업데이트 등)으로 ouid가 변경될 수 있으므로 ouid 기반 서비스 갱신 시 유의해야 합니다.


## 프로젝트 개요
- 본 크로젝트는 넥슨 Open API(FC 온라인)을 기반으로 유저 전적, 매치 상세, 랭커 통계, 메타데이터 및 이미지 자원등 FC 온라인 사용자가 찾는 모든 자원을 통합 제공하는 웹 서비스 'FC-Helper'를 설계 및 구현하는 것을 목표로 합니다.
  시스템은 확장성과 보안성을 고려한 모듈형 아키텍처로 구성되며, 초기 배포는 Docker Compose 기반, 장기적으로는 Kubernetes를 통한 오케스트레이션을 목표로 합니다.


## 주요 가치
1) 개인 전적 확인 및 개인 거래 기록 확인과, 닉네임 검색을 이용한 기본 정보, 최고 등급, 매치 기록등을 검색할 수 있습니다. > 유저 USER
2) 랭커 기반의 메타 분석(인기 선수 / 포지션 / 전술)으로 랭커가 사용하는 메타 정보를 시각적으로 표현합니다. > 랭커 RANKER
3) 신규 사용자 온보딩을 위한 가이드를 제공합니다(팀컬러, 전술, 드리블 등). > 가이드 GUIDE
4) 모든 매치 기록, 매치 상세, 통계 분석등의 기능을 제공합니다. > 경기 MATCH
5) 선수 이미지와 액션 샷 등의 기능을 확인할 수 있습니다. > 이미지 IMAGE
6) 선수별 시즌, 포지션 등의 데이터를 확인할 수 있습니다. > 선수 PLAYER
7) FC온라인의 유명한 방송인과 이벤트 및 대회 등의 컨텐츠를 확인할 수 있습니다. > 커뮤니티 COMMUNITY


## 주요 기능 요약
1) 계정 정보 조회 : 닉네임으로 검색, 기본 정보 조회(닉네임, 레벨), 역대 최고 등급 조회(경기 타입, 디비전, 달성 일자), 유저의 매치 기록 조회(매치 종류(매치 고유 식별자 반환), 해당 매치의 정보), 유저의 거래 기록 조회(본인 기록만 가능 -> 제외)
3) 매치 정보 조회 : 모든 매치 기록 조회, 모든 매치의 매치 고유 식별자 목록 반환, 가장 최근 플레이한 매치부터 내림차순, 매치 종류(matadata/matchtype API), 매치 상세 기록 조회


## 주요 기능 상세 정보 (아래 예시는 openapi.nexon.com 표기 내용을 그대로 발췌하여 작성된 예시 문서이며, 실제 특정 사용자 데이터와는 무관함을 명확히 밝힙니다.)

--------------------------------------------------------------------------------------------------------------------------------------------------------------

1) 계정 정보 조회 (User)
1.1) 계정 식별자(ouid) 조회

엔드포인트: GET /fconline/v1/id
설명: 닉네임으로 유저의 고유 식별자(ouid)를 조회합니다.
필수 파라미터:

x-nxopen-api-key (header)
nickname (query)

응답 예시:

json{
  "ouid": "string"
    }
1.2) 기본 정보 조회

  엔드포인트: GET /fconline/v1/user/basic
  설명: ouid로 유저의 기본 정보(닉네임, 레벨)를 조회합니다.
  필수 파라미터:

  x-nxopen-api-key (header)
  ouid (query)


응답 예시:

json{
      "ouid": "string",
      "nickname": "닉네임",
      "level": 1
    }
    
1.3) 역대 최고 등급 조회

  엔드포인트: GET /fconline/v1/user/maxdivision
  설명: 유저별 역대 최고 등급과 달성일자를 조회합니다.
  필수 파라미터:

  x-nxopen-api-key (header)
  ouid (query)


  응답 예시:

json{
      "matchType": 50,
      "division": 1100,
      "achievementDate": "2023-12-14T22:23:00"
    }
    
1.4) 유저의 매치 기록 조회

  엔드포인트: GET /fconline/v1/user/match
  설명: 유저가 플레이한 매치의 고유 식별자 목록을 반환합니다. 가장 최근 플레이한 매치부터 내림차순으로 정렬됩니다.
  필수 파라미터:

  x-nxopen-api-key (header)
  ouid (query)
  matchtype (query) - 매치 종류 (/metadata/matchtype API 참고)
  offset (query) - 리스트에서 가져올 시작 위치 (기본값: 0)
  limit (query) - 리스트에서 가져올 갯수, 최대 100개 (기본값: 100)


  응답 예시:

  json {
        "64f0a0000a000c2518b00016"
       }

  페이징: offset과 limit을 사용하여 pagination 가능

1.5) 유저의 거래 기록 조회

  엔드포인트: GET /fconline/v1/user/trade
  설명: 거래 종류(tradetype)로 유저의 이적시장 거래 기록을 조회합니다. 본인 거래 기록만 조회 가능
  필수 파라미터:

  x-nxopen-api-key (header)
  tradetype (query) - 거래 종류 (구입: buy, 판매: sell)
  offset (query) - 리스트에서 가져올 시작 위치 (기본값: 0)
  limit (query) - 리스트에서 가져올 갯수, 최대 100개 (기본값: 100)


  응답 예시:

  json
    {
      "tradeDate": "2023-12-14T09:55:45",
      "saleSn": "5dfecf50eff20f2468e00000",
      "spid": 298199236,
      "grade": 1,
      "value": 10000
    }

  페이징: offset과 limit을 사용하여 pagination 가능
  특이사항:

  구매일 경우 구매 등록 시점
  판매일 경우 판매 완료 시점
  가장 최근 거래이력부터 내림차순 정렬


2) 매치 정보 조회 (Match)
2.1) 모든 매치 기록 조회

  엔드포인트: GET /fconline/v1/match
  설명: 모든 매치의 매치 고유 식별자 목록을 반환합니다. 가장 최근 플레이한 매치부터 내림차순으로 정렬됩니다.
  필수 파라미터:

  x-nxopen-api-key (header)
  matchtype (query) - 매치 종류 (/metadata/matchtype API 참고)
  offset (query) - 리스트에서 가져올 시작 위치 (기본값: 0)
  limit (query) - 리스트에서 가져올 갯수, 최대 100개 (기본값: 100)
  orderby (query) - 매치 기록의 정렬 순서 (기본값: desc, 가장 최근 매치부터)


  응답 예시:

  json {
        "64f0a0000a000c2518b00016"
       }

  페이징: offset과 limit을 사용하여 pagination 가능

2.2) 매치 상세 기록 조회

  엔드포인트: GET /fconline/v1/match-detail
  설명: 매치 고유 식별자(matchid)로 매치의 상세 정보를 조회합니다. 매치 시점, 매치 종류와 매치에 참여한 유저의 상세한 매치 통계가 반환됩니다.
  필수 파라미터:

  x-nxopen-api-key (header)
  matchid (query) - 매치 고유 식별자


  응답 구조:

  json{
  "matchId": "64f0000c00007210005f0000",
  "matchDate": "2023-10-29T12:22:48",
  "matchType": 52,
  "matchInfo": [
    {
      "ouid": "string",
      "nickname": "닉네임",
      "matchDetail": {
        "seasonId": 202311,
        "matchResult": "승",
        "matchEndType": 0,
        "systemPause": 0,
        "foul": 0,
        "injury": 1,
        "redCards": 0,
        "yellowCards": 0,
        "dribble": 78,
        "cornerKick": 0,
        "possession": 46,
        "OffsideCount": 1,
        "averageRating": 1.18889,
        "controller": "keyboard"
      },
      "shoot": {
        "shootTotal": 3,
        "effectiveShootTotal": 3,
        "shootOutScore": 0,
        "goalTotal": 2,
        "goalTotalDisplay": 2,
        "ownGoal": 0,
        "shootHeading": 2,
        "goalHeading": 1,
        "shootFreekick": 0,
        "goalFreekick": 0,
        "shootInPenalty": 3,
        "goalInPenalty": 2,
        "shootOutPenalty": 0,
        "goalOutPenalty": 0,
        "shootPenaltyKick": 0,
        "goalPenaltyKick": 0
      },
      "shootDetail": 
        {
          "goalTime": 786,
          "x": 0.9499898435243544,
          "y": 0.4995435465846854,
          "type": 2,
          "result": 1,
          "spId": 272167135,
          "spGrade": 5,
          "spLevel": 5,
          "spIdType": true,
          "assist": false,
          "assistSpId": -1,
          "assistX": 0.5,
          "assistY": 0.5,
          "hitPost": false,
          "inPenalty": true
        }
      ,
      "pass": {
        "passTry": 92,
        "passSuccess": 83,
        "shortPassTry": 55,
        "shortPassSuccess": 55,
        "longPassTry": 3,
        "longPassSuccess": 3,
        "bouncingLobPassTry": 1,
        "bouncingLobPassSuccess": 0,
        "drivenGroundPassTry": 10,
        "drivenGroundPassSuccess": 10,
        "throughPassTry": 19,
        "throughPassSuccess": 14,
        "lobbedThroughPassTry": 4,
        "lobbedThroughPassSuccess": 1
      },
      "defence": {
        "blockTry": 3,
        "blockSuccess": 0,
        "tackleTry": 11,
        "tackleSuccess": 9
      },
      "player": 
        {
          "spId": 277205401,
          "spPosition": 28,
          "spGrade": 3,
          "status": {
            "shoot": 0,
            "effectiveShoot": 0,
            "assist": 0,
            "goal": 0,
            "dribble": 256,
            "intercept": 1,
            "defending": 0,
            "passTry": 12,
            "passSuccess": 11,
            "dribbleTry": 12,
            "dribbleSuccess": 11,
            "ballPossesionTry": 4,
            "ballPossesionSuc": 2,
            "aerialTry": 3,
            "aerialSuccess": 0,
            "blockTry": 0,
            "block": 0,
            "tackleTry": 1,
            "tackle": 1,
            "yellowCards": 0,
            "redCards": 0,
            "spRating": 6.4
          }
        }
        
  ]
}

특이사항:

매치 통계가 생성되기 전에 상대방이 매치를 종료할 경우, 상대방의 매치 정보가 보이지 않을 수 있음
goalTime 계산법:

2^240 ~ 2^241 - 1: 전반전 (그대로 사용)
2^241 ~ 2^242 - 1: 후반전 (2^241 차감 후 4560s 더하기)
2^242 ~ 2^243 - 1: 연장전 전반 (2^242 차감 후 9060s 더하기)
2^243 ~ 2^244 - 1: 연장전 후반 (2^243 차감 후 10560s 더하기)
2^244 ~ 2^245 - 1: 승부차기 (2^244 차감 후 12060s 더하기)


슛 종류:

normal
finesse
header
lob (로빙슛)
flare (플레어슛)
low (낮은 슛)
volley (발리)
free-kick (프리킥)
penalty (페널티킥)
KNUCKLE (무회전슛)
BICYCLE (바이시클킥)
super (파워샷)


슛 결과: 1 (ontarget), 2 (offtarget), 3 (goal)
matchEndType: 0 (정상종료), 1 (몰수승), 2 (몰수패)
controller: keyboard / pad / etc


3) 랭커 정보 조회 (Ranker)
3.1) TOP 10,000 랭커 선수 통계 조회

엔드포인트: GET /fconline/v1/ranker-stats
설명: TOP 10,000 랭커 유저가 사용한 선수의 20경기 평균 스탯을 조회합니다.
필수 파라미터:

x-nxopen-api-key (header)
matchtype (query) - 매치 종류 (/metadata/matchtype API 참고)
players (query) - 조회하고자 하는 선수 목록 (URL 인코딩한 JSON Array)


players 파라미터 형식:

"id"와 "po" 필드를 갖고 있는 JsonArray를 URL 인코딩한 값
id: 선수 고유 식별자 (spid, /metadata/spid API 참고)
po: 선수 포지션 (spposition, /metadata/spposition API 참고)
예시: [{"id":100167680,"po":18}] → %5B%7B%22id%22%3A100167680%2C%22po%22%3A18%7D%5D


응답 예시:

json
  {
    "spId": 100167680,
    "spPosition": 18,
    "status": {
      "shoot": 0,
      "effectiveShoot": 0,
      "assist": 0,
      "goal": 0,
      "dribble": 165,
      "dribbleTry": 9,
      "dribbleSuccess": 9,
      "passTry": 14,
      "passSuccess": 11,
      "block": 0,
      "tackle": 0,
      "matchCount": 1
    },
    "createDate": "2023-12-14T01:12:34"
  }


주의사항:

한번에 너무 많은 선수목록을 입력할 경우 413 Request Entity Too Large 에러가 반환될 수 있음
권장: 1회 50명 적합


4) 메타데이터 정보 조회 (Metadata)
4.1) 매치 종류(matchtype) 메타데이터 조회

엔드포인트: GET /static/fconline/meta/matchtype.json
설명: 매치 종류(matchtype) 메타데이터를 조회합니다.
파라미터: 없음 (요청 파라미터 입력 없이 조회 가능)
응답 예시:

json
  {
    "matchtype": 30,
    "desc": "리그 친선"
  }

4.2) 선수 고유 식별자(spid) 메타데이터 조회

엔드포인트: GET /static/fconline/meta/spid.json
설명: 선수 고유 식별자는 시즌 아이디(seasonid) 3자리 + 선수 아이디(pid) 6자리로 구성되어 있습니다.
파라미터: 없음
응답 예시:

json
  {
    "id": 100000051,
    "name": "앨런 시어러"
  }


주의사항: 시즌 아이디는 /metadata/seasonid API로 조회할 수 있습니다.

4.3) 시즌 아이디(seasonId) 메타데이터 조회

엔드포인트: GET /static/fconline/meta/seasonid.json
설명: 시즌 아이디는 선수가 속한 클래스를 나타냅니다.
파라미터: 없음
응답 예시:

json
  {
    "seasonId": 100,
    "className": "ICONTM (ICON The Moment)",
    "seasonImg": "https://ssl.nexon.com/s2/game/fc/online/obt/externalAssets/season/icontm.png"
  }

4.4) 선수 포지션(spposition) 메타데이터 조회

엔드포인트: GET /static/fconline/meta/spposition.json
설명: 선수 포지션(spposition) 메타데이터를 조회합니다.
파라미터: 없음
응답 예시:

json
  {
    "spposition": 0,
    "desc": "GK"
  }

4.5) 등급 식별자(division) 메타데이터 조회

엔드포인트: GET /static/fconline/meta/division.json
설명: 등급 식별자(division) 메타데이터를 조회합니다.
파라미터: 없음
응답 예시:

json
  {
    "divisionId": 800,
    "divisionName": "슈퍼챔피언스"
  }

4.6) 볼타 공식경기 등급 식별자 메타데이터 조회

엔드포인트: GET /static/fconline/meta/division-volta.json
설명: 볼타 공식경기의 등급 식별자(division) 메타데이터를 조회합니다.
파라미터: 없음
응답 예시:

json
  {
    "divisionId": 1100,
    "divisionName": "월드 스타"
  }


5) 이미지 정보 조회 (Image)
5.1) 선수 이미지 조회 (spid 기반)

엔드포인트: GET /live/externalAssets/common/players/p{spid}.png
설명: 선수 고유 식별자(spid)로 선수의 이미지를 가져옵니다.
파라미터:

spid (path) - 선수 고유 식별자 (/metadata/spid API 참고)


응답: 선수 이미지 (image/png)
주의사항: 특정 선수들은 이미지가 존재하지 않을 수 있습니다.

5.2) 선수 이미지 조회 (pid 기반)

엔드포인트: GET /live/externalAssets/common/players/p{pid}.png
설명: pid로 선수의 이미지를 가져옵니다.
파라미터:

pid (path) - 선수 식별자 (spid 뒤 6자리)


응답: 선수 이미지 (image/png)
주의사항:

특정 선수들은 이미지가 존재하지 않을 수 있습니다.
(pid)가 "0"으로 시작하는 경우, 시작 부분의 0을 모두 제외해야 정상적으로 조회 가능 (예: "000401" → "401")



5.3) 선수 액션샷 이미지 조회 (spid 기반)

엔드포인트: GET /live/externalAssets/common/playersAction/p{spid}.png
설명: 선수 고유 식별자(spid)로 선수의 액션샷 이미지를 가져옵니다.
파라미터:

spid (path) - 선수 고유 식별자


응답: 선수 액션샷 이미지 (image/png)
주의사항: 특정 선수들은 이미지가 존재하지 않을 수 있습니다.

5.4) 선수 액션샷 이미지 조회 (pid 기반)

엔드포인트: GET /live/externalAssets/common/playersAction/p{pid}.png
설명: pid로 선수의 액션샷 이미지를 가져옵니다.
파라미터:

pid (path) - 선수 식별자


응답: 선수 액션샷 이미지 (image/png)
주의사항:

특정 선수들은 이미지가 존재하지 않을 수 있습니다.
(pid)가 "0"으로 시작하는 경우, 시작 부분의 0을 모두 제외해야 정상적으로 조회 가능


## 현재 구현 불가능한 기능 및 대응 방안

1) 유저의 거래 기록 조회 제한사항
문제: API는 본인의 거래 기록만 조회 가능합니다. 즉, 다른 유저의 거래 기록을 볼 수 없습니다.
대응 방안:

OAuth 연동: 사용자가 넥슨 계정으로 로그인하여 자신의 API 키를 제공받도록 구현
사용자 본인 계정으로 로그인 시에만 "내 거래 기록" 메뉴 활성화
구현 흐름:

사용자가 넥슨 계정으로 OAuth 로그인
넥슨에서 제공하는 사용자 전용 API 키 받기
해당 API 키를 안전하게 저장 (암호화)
사용자가 자신의 거래 기록 조회 시 해당 API 키 사용



2) 실시간 이적 시장 현황
문제: Nexon Open API에는 이적 시장 실시간 매물 조회 API가 존재하지 않습니다.
대응 방안:

크롤링 불가: 넥슨 이용약관 및 API 정책상 크롤링은 금지되어 있음
제한적 구현: 유저의 거래 기록을 기반으로 최근 거래된 선수들의 가격 추이를 분석하는 방식으로 대체
대안:

TOP 10,000 랭커들이 최근 거래한 선수 집계
특정 선수의 최근 30일간 거래 가격 추이 그래프 제공
"급등/급락 선수" 분석은 제한적으로만 가능



3) 실시간 데이터 제약
문제: API 데이터는 2시간 전 데이터까지만 제공됩니다.
대응 방안:

UI에 명확한 안내 문구 표시: "데이터는 2시간 전까지 업데이트됩니다"
캐싱 전략 조정: 최신 데이터를 자주 갱신할 필요 없음 (TTL 30분~1시간)


구현 가능한 추가 기능 아이디어
1) 유저 비교 기능

설명: 두 유저의 닉네임을 입력받아 전적을 비교
구현 방법:

두 유저의 ouid 조회
각각의 최고 등급, 최근 30경기 통계 조회
승률, 평균 득점, 점유율 등을 시각화하여 비교



2) 선수 추천 시스템

설명: 랭커 데이터 기반으로 포지션별 인기 선수 추천
구현 방법:

ranker-stats API로 TOP 10,000 랭커들의 선수 사용 데이터 수집
포지션별로 가장 많이 사용된 선수 TOP 50 집계
승률이 높은 선수 필터링
사용자의 예산대에 맞는 선수 추천



3) 매치 리플레이 분석

설명: 특정 매치의 상세 데이터를 시각화
구현 방법:

match-detail API로 슛 좌표, 패스 성공률 등 수집
경기장 히트맵 생성 (어느 구역에서 슛을 많이 쐈는지)
시간대별 득점 패턴 분석



4) 나의 전적 트래킹

설명: 사용자가 자신의 닉네임을 등록하면 정기적으로 전적 갱신
구현 방법:

사용자가 닉네임 등록
배치 스케줄러로 매일 자정에 해당 유저의 최근 매치 수집
승률 변화, 등급 변화 추이 그래프 제공
목표 등급 설정 시 달성률 표시



5) 팀 구성 시뮬레이터

설명: 선수를 선택하여 가상의 팀을 구성하고 팀 오버롤 계산
구현 방법:

metadata API로 선수 정보 로드
사용자가 포지션별로 선수 배치
팀 컬러 계산 (같은 리그/국적 보너스)
예상 팀 오버롤 및 급여 시스템 표시


제약: 정확한 팀 컬러 보너스 수치는 역공학 필요 (API 미제공)

6) 메타 분석 대시보드

설명: 현재 메타(유행하는 전술/선수)를 시각화
구현 방법:

ranker-stats API로 랭커들의 선수 사용률 집계
포지션별 TOP 10 선수 표시
전주 대비 사용률 변화 표시
"급부상 선수" / "하락세 선수" 표시


## 데이터 스키마 설계

User 테이블
sqlCREATE TABLE user (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    ouid VARCHAR(50) UNIQUE NOT NULL,
    nickname VARCHAR(30) NOT NULL,
    level INT,
    max_division INT,
    max_division_date DATETIME,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_nickname (nickname),
    INDEX idx_ouid (ouid)
);
Match 테이블
sqlCREATE TABLE match (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    match_id VARCHAR(50) UNIQUE NOT NULL,
    user_ouid VARCHAR(50) NOT NULL,
    match_type INT NOT NULL,
    match_date DATETIME NOT NULL,
    result VARCHAR(10),
    goal_total INT,
    possession INT,
    shoot_total INT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_ouid (user_ouid),
    INDEX idx_match_id (match_id),
    INDEX idx_match_date (match_date),
    FOREIGN KEY (user_ouid) REFERENCES user(ouid) ON DELETE CASCADE
);
MatchDetail 테이블
sqlCREATE TABLE match_detail (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    match_id VARCHAR(50) NOT NULL,
    ouid VARCHAR(50) NOT NULL,
    season_id INT,
    match_result VARCHAR(10),
    match_end_type INT,
    foul INT,
    yellow_cards INT,
    red_cards INT,
    corner_kick INT,
    average_rating DOUBLE,
    controller VARCHAR(20),
    shoot_total INT,
    goal_total INT,
    pass_try INT,
    pass_success INT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_match_id (match_id),
    FOREIGN KEY (match_id) REFERENCES match(match_id) ON DELETE CASCADE
);
RankerStats 테이블
sqlCREATE TABLE ranker_stats (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    sp_id INT NOT NULL,
    sp_position INT NOT NULL,
    match_type INT NOT NULL,
    avg_shoot DOUBLE,
    avg_goal DOUBLE,
    avg_assist DOUBLE,
    avg_pass_success DOUBLE,
    match_count INT,
    collected_date DATETIME NOT NULL,
    INDEX idx_sp_id (sp_id),
    INDEX idx_match_type (match_type),
    INDEX idx_collected_date (collected_date)
);
Metadata 테이블 (캐싱용)
sqlCREATE TABLE metadata_cache (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    meta_type VARCHAR(50) NOT NULL,
    meta_key VARCHAR(100) NOT NULL,
    meta_value TEXT NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    UNIQUE KEY unique_meta (meta_type, meta_key),
    INDEX idx_meta_type (meta_type)
);

API 응답 코드 (공통)
모든 API는 다음 응답 코드를 반환할 수 있습니다:

200: SUCCESS
400: Bad Request (잘못된 파라미터)
403: Forbidden (API Key 인증 실패)
429: Too Many Requests (Rate Limit 초과)
500: Internal Server Error (서버 오류)

에러 응답 형식:
json{
  "error": {
    "name": "string",
    "message": "string"
  }
}
