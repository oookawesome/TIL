## NSIS를 이용한 인스톨러 구성 방법

### Install
- NSIS 설치 : [링크](https://sourceforge.net/projects/nsis/)
- HS NIS Edit 설치 : [링크](http://hmne.sourceforge.net/)

### 마법사를 이용한 인스톨러 생성
1. HS NIS Edit 실행
2. 스크립트 작성 마법사(ctrl+w) 실행
3. 프로그램 정보: : 이름, 버전.. 입력
4. 설치옵션 : 설치파일 명 입력, 설치 언어 선택
5. 프로그램 디렉토리 및 라이센스 : 프로그램 기본 설치 경로 및 라이센스 파일 경로 설정
6. 프로그램 파일 : 우측 흰 파일아이콘 클릭 > 설치할 파일들 선택
7. 프로그램 아이콘 : 디폴트 선택
8. 설치 후 실행 : 디폴트 선택
9. 언인스톨러 : 디폴트 선택 
10. 마법사 완료 : 스크립트 저장 체크 및 저장
11. 에디터 상단 파란 아이콘(스크립트 컴파일 및 실행) 클릭
12. 정상 수행 시 스크립트 파일 경로에 인스톨러 생성 완료

### 콘솔 명령으로 실행하는 방법
- makensis.exe 파일을 실행
- \$ {NSIS 설치 경로}/makensis.exe {생성한 nsi스크립트 파일 경로}

### 커스텀 인스톨 페이지 추가 방법
- EX) 인스톨러에 파일 설치 완료 후, 추가로 배치파일을 실행하는 페이지를 넣고 싶은 경우
- __InstallOptions 라이브러리를 이용하여 구현__

#### Steps
1. InstallOptions를 스크립트 파일에 임포트
    - `!include InstallOptions.nsh`
2. 페이지 추가 
    - `MUI 화면 정의 코드부(!insertmacro ~ 코드가 있는 부분)에 코드 추가
    - `Page custom CustomPage LeaveCustomPage` 
3. CustomPage 정의 
    - 화면에 보여줄 부분 정의
    - Function 명령으로 해당 함수내용을 정의
    - 함수 내부에 화면코드 추가
    - `!insertmacro INSTALLOPTIONS_DISPLAY ${INIPATH}`
    ```
    Function CustomPage
    !insertmacro INSTALLOPTIONS_DISPLAY ${INSTALLMODE_INIPATH}
    FunctionEnd
    ```
4. INI파일 정의
    - 커스텀 페이지의 표시될 내용은 스크립트 내부가 아닌 INI파일을 생성하여 해당 파일에 정의해야 함. 이 INI파일의 내용을 읽어와 인스톨러 페이지를 구성하게 됨.
    - INI파일의 경우, 반드시 [Settings]가 포함되어야 하고, 필드 추가 시 [Field 1]과 같은 형식으로 작성해야 함
    - ex. 그룹 박스와 라디오버튼 1개를 구성하려는 경우
    ```
    [Settings] 
    NumFields=2 // 필드 갯수와 일치하는 지 확인 필요, 맞지 않으면 필드가 보이지 않음
    Title=title!
    NextButtonText=Next
    BackButtonText=Back
    
    [Field 1]
    Type=Groupbox
    Text=addtional packages MUST be installed. Select Install Type
    Flags=NOTABSTOP
    Left=30 // 표시 위치 지정
    Right=270
    Top=0
    Bottom=54

    [Field 2]
    Type=RadioButton
    Text=offline
    Flags=NOTIFY
    State=1
    Left=46
    Right=122
    Top=14
    Bottom=25
    ```
5. 유저 선택에 대한 로직 구성
    - LeaveCustomPage 정의
    - 유저가 선택한 내용(ex. 라디오 버튼을 선택한 것)에 대해 처리하는 부분을 구성. 
    - 유저가 선택한 내용은 INI파일에 저장되고, 해당 파일의 state필드값에서 결과를 가져올 수 있음.
    ```
    Function LeaveCustomPage
    !insertmacro INSTALLOPTIONS_READ $0 ${INIPATH} "Settings" "State" 
    StrCmp $0 0 validate // NextButton을 페이지에서 누른 경우, state가 0임
    Abort

    validate:
    !insertmacro INSTALLOPTIONS_READ $0 ${INIPATH} "Field 2" "State"
    StrCmp $0 1 offlineInst

    offlineInst:
    Call offlineInstall
    Goto done

    done:
    FunctionEnd
    ```


### 주요 스크립트 문법
- Constant설정 : `!define SAMPLE "sample"`
- 콘솔 명령어 실행 
    ```
    ReadEnvStr $0 COMSPEC
    Exec '$0 /C python "$TEMP\robotspaceinstall\Supports\install_addtional_package.py"'
    ``` 

---
#### 참고
- [Readme](http://nsis.sourceforge.net/Docs/InstallOptions/Readme.html)
- [커스텀 페이지 샘플 코드](http://nsis.sourceforge.net/Add-on_Custom_Installer_Sample_/w_InstallOptions)
