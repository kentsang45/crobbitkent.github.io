---
layout: post
title:  "기상청 동네예보 API 사용해보기"
date:   2020-01-05
---

#### 1. 요구사항
자바 프로그래밍만 공부하다가 API를 직접 쓰고 사이트를 구축해보고 싶었습니다. 

- 지금의 날씨, 현 시간부터 3시간 단위로 나오는 오늘의 날씨, 주간 날씨  

이렇게 세가지 기능을 넣는 것이 목표였습니다.

세 기능을 넣기 위해 필요한 API는 2개였습니다.

그리고 하나의 API에 여러 기능을 제공하고 있기에, 내부기능만 따지면 총 4개의 내부기능을 사용했습니다.

첫번째 API : 동네예보 조회서비스 (초단기예보조회, 동네예보조회)
두번째 API : 중기예보 조회서비스 (중기육상예보조회, 중기기온조회)

지금의 날씨를 구현하기 위해 '초단기예보조회'를 사용했다. 
'초단기예보조회'에서는 현재의 강수 형태, 하늘 상태, 온도를 받았다.
'동네예보조회'에서는 3시간 마다의 강수 형태, 하늘 상태, 온드를 받는다.
'중기육상예보조회'에서는 3일후~10일후의 하늘 상태와 강수 확률을 받는다.
'중기기온조회'에서는 3일후~10일후의 최저온도와 최대온도를 받았다.

API에서 받은 다양한 정보들을 모아서 내가 원하는 기능이 나오도록 구현하였다.


#### 2. API 받아오는 법
https://data.go.kr/index.do
공공데이터포털 사이트에 회원가입을 하고 접속한다.
동네예보조회 API와 중기예보 조회 API를 검색하고 활용신청한다.

##### APIParser 클래스
##### 1) API를 위한 주소를 만든다.
```{.java]}
        StringBuilder urlBuilder = new StringBuilder(apiBaseURL[type.ordinal()]);  
        urlBuilder.append("?" + URLEncoder.encode("serviceKey", "UTF-8") + "=" + serviceKey);  
        appendUrlBuilder(urlBuilder, "&", "numOfRows", "500");  
        appendUrlBuilder(urlBuilder, "&", "pageNo", "1");  
        appendUrlBuilder(urlBuilder, "&", "dataType", "JSON");  

        // API 주소를 2개로 나누기
        if(APIType.MID == type || APIType.MIDLAND == type){  
            appendUrlBuilder(urlBuilder, "&", "regId", (APIType.MID == type) ? regId1 : regId2);  
            appendUrlBuilder(urlBuilder, "&", "tmFc", base_date + base_time); // 기준 날짜와 시간  
        }else {  
            appendUrlBuilder(urlBuilder, "&", "base_date", base_date); // 기준 날짜  
            appendUrlBuilder(urlBuilder, "&", "base_time", base_time); // 기준 시간  
            appendUrlBuilder(urlBuilder, "&", "nx", dao.getTodayWeather().getNx());  
            appendUrlBuilder(urlBuilder, "&", "ny", dao.getTodayWeather().getNy());  
        }  

    private static void appendUrlBuilder(StringBuilder builder, String mark, String a1, String a2) throws IOException {  
      builder.append(mark + URLEncoder.encode(a1, "UTF-8") + "=" + URLEncoder.encode(a2, "UTF-8"));  
    }  
```

##### 2) http로 연결하기
```{.java]}
        URL url = new URL(urlBuilder.toString());  
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();  
        conn.setRequestMethod("GET");  
        conn.setRequestProperty("Content-type", "application/json");  

        BufferedReader reader;  

        if (200 <= conn.getResponseCode() && 300 >= conn.getResponseCode()) {  
            reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));  
        } else {  
            reader = new BufferedReader(new InputStreamReader(conn.getErrorStream()));  
        }  

        StringBuilder sb = new StringBuilder();  

        String line = "";  

        while (null != (line = reader.readLine())) {  
            sb.append(line);  
        }  

        reader.close();  
        conn.disconnect();  
```

##### 3) 연결해서 나온 값을 JSON형식으로 저장한다.
```{.java]}  
        String jsonData = sb.toString();  

        JSONObject obj = new JSONObject();  
        JSONParser jsonParser = new JSONParser();  
        JSONObject jsonObject = (JSONObject) jsonParser.parse(jsonData);  
        JSONObject parse_response = (JSONObject) jsonObject.get("response");  
        JSONObject parse_body = (JSONObject) parse_response.get("body");  
        JSONObject parse_items = (JSONObject) parse_body.get("items");  
        
        return (JSONArray) parse_items.get("item");  
```

##### 4) JSONArray를 순회하며 Weather라는 클래스에 정보를 주입한다.
```{.java]}  
    // 초단기 예보 API를 통해 현재 날씨를 설정  
	private void getShortForecast() throws IOException, ParseException {  
		String baseDate = LocalDateTime.now().format(DateTimeFormatter.BASIC_ISO_DATE);  
		String orgTime = LocalDateTime.now().format(DateTimeFormatter.ofPattern("HH")) + "00";  
		int intTime = (Integer.parseInt(orgTime) - 100);  
		String baseTime = (intTime < 1000) ? "0" + intTime + "" : intTime + "";  

		JSONArray jsonArray = APIParser.apiSetUp(APIType.ULTRA_SHORT, this, baseDate, baseTime);  

		int size = jsonArray.size();  

		for (int i = 0; i < size; ++i) {  
			JSONObject weatherObj = (JSONObject) jsonArray.get(i);  
			Weather weather = todayWeather;  
			// 오늘 날짜  
			if (true == weatherObj.get("fcstTime").equals(orgTime)) {  
				// 카테고리 별로 나눠서 정보를 받고 설정한다.  
				// 강수 상태  
				if (true == weatherObj.get("category").equals("PTY")) {  
					weather.setPty((String) weatherObj.get("fcstValue"));  
				} else if (true == weatherObj.get("category").equals("SKY")) {  
					weather.setSky((String) weatherObj.get("fcstValue"));  
				} else if (true == weatherObj.get("category").equals("T1H")) {  
					weather.setT1h((String) weatherObj.get("fcstValue")); // T1H 정보 추출  
				}  
			}  
		} // for end  
	}  
```



