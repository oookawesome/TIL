# ElastAlert를 이용한 ElasticSearch 로깅 알람 설정

## ElastAlert
Yelp에서 개발한 오픈소스 라이브러리로, Elastic Search의 데이터 패턴에 대한 알림을 설정할 수 있다. 다양한 커스터마이징을 제공함

## 사용 배경 
Elastic Search에서 공식 제공하는 Alerting 플러그인은 X-Pack에 포함된 것으로, __유료임__

## Setting
- pip 패키지 설치
	```
	$pip install elastalert
	$pip install elasticsearch # 설치된 Elastic버전에 맞는 지 확인필요
	```
- config 설정
	- [elastalert git hub](https://github.com/Yelp/elastalert])에서 config.yaml.example 복사
	- 설정값 세팅
		- 파일명 : config.yaml
		- rules_folder : rule yaml이 있는 폴더
		- run_every : query 조회 시간 간격
		- es_host, es_port : elastic 서버 주소 및 port
- rule 설정
	- [elastalert git hub](https://github.com/Yelp/elastalert])에서 example_rules/example.frequency.yaml을 rules폴더로 복사
	- 설정값 세팅
		- 파일명 : default_rules.yaml
		- es_host, es_port : elastic 서버 주소 및 port
		- use_ssl
		- type : alert type 설정
			- type 종류는 [doc참조](https://elastalert.readthedocs.io/en/latest/ruletypes.html#rule-types)
			- any : 매칭되면 알림
		- index : 조회할 elastic 인덱스
			- aiserver_prod-*
		- filter : elastic search 필터 설정
			- [es 웹페이지 참조](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)
			- query_string
				- query: "message: ERROR"
		- alert : 알림 방법. 자세한 방법은 [doc참조](https://elastalert.readthedocs.io/en/latest/ruletypes.html#alerts)
			- command : python command 모듈의 arg 형태로 인자를 받아 command 수행
				- 인자 : \['python', 'send_api.py'\]
			- JIRA, Slack 등 다양한 협업 도구의 알림을 지원한다고 함

## 설정 완료 확인
```
$elastalert-test-rule --config config.yaml rules/default_rules.yaml
```
- AI서버의 경우, 환경변수로 HTTP_PROXY가 세팅되어있어 동작이 안됨. 콘솔 명령을 통해 해당 값을 해제해야 함
	- $set HTTP_PROXY=

# 실행 
```
$python -m elastalert.elastalert --config config.yaml --verbose --rule rules\default_rules.yaml
```
