---
lab:
    title: 'LAB 02_Function Apps'
    module: '모듈 02 - 플랫폼 보호'
---

# 랩: Function Apps

Azure Function Apps는 Azure App Service 인프라를 사용합니다. 이 랩에서는 Azure Portal에서 Function App을 만드는 방법을 알아봅니다. Function App은 개별 함수의 실행을 호스팅하는 컨테이너입니다. Function App을 App Service Plan에 만들면 Function App이 App Service의 모든 기능을 사용할 수 있습니다.

### 연습 1: Function과 트리거 생성

#### 작업 1: 랩 설정

1. 웹 브라우저에 [이 URL](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoftLearning%2FAZ-500-Azure-Security%2Fmaster%2FAllfiles%2FLabs%2FMod2_Lab02%2Ftemplate.json)로 접속한다.

1. 리소스 그룹에서 **새로 만들기**를 선택하고 `az5000202`를 입력한다.

1. **위에 명시된 사용 약관에 동의함** 체크박스에 체크를 한 후 **구매**버튼을 클릭한다.


#### 작업 2: Function App에 HTTP 트리거 추가

1. **리소스 그룹**으로 이동한다.

1. 이전에 `작업 1`에서 배포한 리소스의 배포가 완료되면 **az5000202**로 이동한다.

1. 생성된 Function App을 선택한다.

    > **참고**: 현재 Function App에 지정된 함수는 없습니다.

1. **개요** 블레이드의 상단 메뉴에서 **</> Switch to classic experience**를 선택하고, **-> 클래식 환경으로 계속**을 클릭한다.

1. **함수**를 클릭한다.

1. 상단에 **새 함수**를 클릭한다.

1. 상단에 **실험적 언어 지원**을 클릭하여 **사용**으로 변경한다.

    > **참고**: 다른 언어가 트리거에 추가되었습니다.

1. **HTTP trigger**를 클릭한다.

1. 오른쪽에 **새 함수** 컨텍스트 창이 뜨면 언어를 **PowerShell**로 수정한다.

1. 이름은 기본값으로 둔다.

1. **권한 수준**은 **Function**으로 설정한다.

1. **만들기** 버튼을 클릭한다.

PS1 HTTP 트리거가 생성되었습니다.

#### 작업 3: HTTP 트리거를 이용하여 REST 호출 테스트

1. Function App 이름을 확인한다. (**function app 아이콘 옆**).

1. **HTTP trigger** Function에서 **관리**를 클릭한다.

1. 함수 키에 있는 **default**의 작업에서 복사 버튼을 클릭하여 키를 복사한다. 복사한 키는 별도로 메모해 둔다.

1. 다음 URL로 접속하여 PowerShell 코드를 복사한다.

    ```cli
    https://raw.githubusercontent.com/godeploy/AZ500/master/AZ500%20Mod2%20Lab%202/RESTgetHTTPtrigger.ps1
    ```

1. **PowerShell ISE**를 실행하여 코드를 붙여넣기 한다. 

1. `$functionappname = ""` 변수에 생성된 Function App 이름을 입력한다.

1. `$functionkey = ""` 변수에 앞서 메모해둔 함수 키를 입력한다.

1. PowerShell 스크립트를 실행한다. (F5)

1. **PowerShell ISE** 결과창에 다음과 같은 결과가 나타나는지 확인한다.

    ```powershell
    This is result of a normal GET operation calling your HTTP trigger
    Hello 

    This is result of a normal GET operation calling your HTTP trigger with an extra parameter passed to the trigger
    Hello World!

    This is result of a normal PUT operation calling your HTTP trigger that feeds a hash table converted to JSON to the HTTP triggger
    Hello Max Power
    ```

HTTP 트리거를 가지는 Function App을 생성하고 PowerShell 스크립트를 이용하여 Function App을 테스트 해봤습니다.

### 연습 2: 랩 리소스 삭제

#### 작업 1: Cloud Shell 열기

1. Azure 포털 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 연다.

1. Cloud Shell 인터페이스에서 **Bash**를 선택한다.

1. **Cloud Shell** 명령 프롬프트에서 다음 명령을 입력하고 **Enter**를 눌러 이 랩에서 생성한 모든 리소스 그룹을 나열한다.

   ```sh
   az group list --query "[?starts_with(name,'az500')].name" --output tsv
   ```

1. 출력된 결과가 이 랩에서 생성한 리소스 그룹만 포함되어 있는지 확인한다. 이 그룹은 다음 작업에서 삭제된다.

#### 작업 2: 리소스 그룹 삭제하기

1. **Cloud Shell** 명령 프롬프트에서 다음 명령을 입력하고 **Enter**를 눌러 이 랩에서 생성한 모든 리소스 그룹을 삭제한다.

   ```sh
   az group list --query "[?starts_with(name,'az500')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. **Cloud Shell** 명령 프롬프트를 닫는다.

> **결과**: 이 연습을 완료한 후 이 랩에서 사용된 리소스 그룹을 제거했습니다.
