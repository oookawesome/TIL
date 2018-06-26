## JenkinsFile을 활용한 Jenkins Pipeline 설정방법

### 시작 전에..
Jenkins 2.x 이상에서는 build item으로 Pipeline 형식을 제공하고, Pipeline은 Groovy와 같은 DSL로 작성된 파일을 바탕으로, Code기반의 자동화 테스트 표현을 가능하게 해준다.

> JenkinsFile : Pipeline설정을 정의한 Groovy파일. 해당 파일에 Pipeline설정을 작성한다.

### 개념
- Pipeline : 파이프라인은 CD Pipeline 모델을 나타낸다. 해당 부분의 코드는 전체 빌드 프로세스를 정의한다. 일반적으로 빌드, 테스트, 배포 등의 Stage를 포함하는 형식이 된다.
- Node : Pipeline을 수행할 Jenkins 머신을 나타낸다.
- Stage : 빌드, 테스트 등과 같은 개념적으로 분리된 Subset을 나타낸다.
- Step : 단일 Task를 의미한다. (ex. sh 'make')
- overview :
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                //
            }
        }
        stage('Test') {
            steps {
                //
            }
        }
        stage('Deploy') {
            steps {
                //
            }
        }
    }
}
```

### script 주요 명령어
- sh : bash쉘에서 쉘 스크립트 실행 (ex. sh 'gradlew test')
- __bat : windows에서 쉘 스크립트 실행 (ex. bat 'gradlew test')__
- echo : 콘솔 출력
- sleep : 슬립 (ex. sleep 30)
- pwd : 현재 파일 디렉토리 경로 출력
- try-catch문 사용가능

### Example (Windows에서 jenkins구동)
```
pipeline {
    agent any

    stages {
        stage('Kill previous instance') {
            steps {
                
            }
        }

        stage('unit test') {
            steps {
                bat 'gradlew clean test'
            }
        }

        stage('component test') {
            steps {
                bat 'gradlew componentTest'
            }
        }

        stage('contract test') {
            steps {
                script {
                    String path = pwd() // 현재 경로 출력
                    try {
                        bat 'gradlew contractTest'
                    }
                    // 테스트 실패 시 결과 업로드
                    catch (err) {
                        bat 
                    }
                }
            }
        }

        stage('update contract test result') {
            steps {
                script {
                    String path = pwd()
                }
            }
        }

        stage('build') {
            steps {
                bat 'gradlew build'
            }
        }

        stage('run service') {
            steps {
                bat 'start java -jar build/libs/ho-0.0.1-SNAPSHOT.jar'
            }
        }

    }
}
```

### CI 설정 방법
1. 작성한 JenkinsFile.groovy 파일을 프로젝트 루트에 저장
2. Jenkins 서버 접속 > 새로운 Item > Pipeline 선택
3. Pipeline 탭 > Definition > Pipeline Script from SCM 선택
4. SCM > Git선택
5. Repository URL/Credential/Branch 설정
6. Script Path > JenkinsFile.groovy 입력
(JenkinsFile을 루트에 놓지 않은 경우, 경로 지정)
---
#### 참고
- [파이프라인 개요](https://jenkins.io/doc/book/pipeline/)
- [명령어 참조](https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/)
- [샘플 코드](https://jenkins.io/doc/pipeline/examples/)
