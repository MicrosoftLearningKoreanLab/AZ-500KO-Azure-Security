---
lab:
    title: '랩 2 - 함수 앱'
    module: '모듈 2 - 플랫폼 보호 구현'
---

# 모듈 2: 랩 2 - 함수 앱


Azure Function 앱은 Azure App Service 인프라를 사용합니다. 이 항목에서는 Azure Portal에서 함수 앱을 만드는 방법을 설명합니다. 함수 앱은 개별 함수 실행을 호스트하는 컨테이너입니다. App Service 호스팅 계획에서 만드는 함수 앱은 App Service의 모든 기능을 사용할 수 있습니다.

## 연습 1: 함수 및 트리거 만들기

### 태스크 1: 랩 설정

1.  PowerShell을 열고 다음 명령을 실행합니다.

     ```powershell
    start "https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoftLearning%2FAZ-500-Azure-Security%2Fmaster%2FAllfiles%2FLabs%2FMod2_Lab02%2Ftemplate.json" 
     ```

2.  리소스 그룹 섹션에서 **새로 만들기** 를 클릭합니다.
3.  이름으로 **myResourceGroup** 을 입력하고 **확인** 을 클릭합니다.
4.  블레이드 아래쪽의 확인란을 선택하여 사용 약관에 동의합니다.
5.  **구매** 를 선택합니다.


### 태스크 2: 함수 앱에 HTTP 트리거 추가

1.  리소스 그룹을 선택합니다.

1.  랩 설정에서 만든 리소스 그룹을 선택합니다.

1.  리소스 그룹에 작성된 함수 앱 템플릿을 선택합니다.
경고
**참고**: 현재는 함수 앱에 할당된 함수가 없습니다.


1.  **함수** 를 클릭합니다.

1.  **새 함수** 를 클릭합니다.

1.  오른쪽 위에서 실험적 언어 지원 슬라이드 단추를 클릭합니다.
경고
**참고**: 다른 언어가 트리거에 추가되었습니다.


1.  **HTTP 트리거** 를 선택합니다.

1.  언어를 **PowerShell** 로 변경합니다.

1.  이름을 기본값을 그대로 유지합니다.

1.  **권한 부여** 수준이 함수로 설정되어 있는지 확인합니다.

1.  **만들기** 를 클릭합니다.

 PS1 HTTP 트리거 템플릿이 작성되었습니다. 템플릿 코드가 표시되지 않으면 페이지를 새로 고치세요. 

### 태스크 3: HTTP 트리거에 대한 REST 호출 테스트

1.  **함수 앱 아이콘 옆** 에 있는 함수 앱의 이름을 적어 둡니다.

1.  **HTTP 트리거** 함수 아래에서 **관리** 를 클릭합니다.

1.  함수 키 아래에서 기본 함수 키의 작업 아래에 있는 복사를 선택한 다음 복사한 키를 메모장 파일에 저장합니다.

1.  **PowerShell ISE** 를 열고 다음 **URL** 에서 PowerShell 코드를 복사합니다.

     ```cli
    https://github.com/GoDeploy/AZ500/blob/master/AZ500%20Mod2%20Lab%202/RESTgetHTTPtrigger.ps1
     ```

1.  $functionappname = "" 변수에 함수 앱 이름을 입력합니다.

1.  $functionkey = "" 변수에는 이전 단계를 수행할 때 Portal에서 복사한 긴 함수 키를 입력합니다.

1.  PowerShell 스크립트를 실행합니다.

1.  **ISE**의 결과에 다음 출력이 표시됩니다.

    ```powershell
    This is result of  a normal GET operation calling your HTTP trigger
    Hello 

    This is result of  a normal GET operation calling your HTTP trigger with an extra parameter passed to the trigger
    Hello World!

    This is result of a normal PUT operation calling your HTTP trigger that feeds a hash table converted to JSON to the HTTP triggger
    Hello Max Power
    ```

**결과**: HTTP 트리거 함수 앱을 빌드하고 REST 기반 명령을 사용하여 해당 앱과 통신했습니다.


| 경고: 계속하기 전에 이 랩에서 사용한 모든 리소스를 제거해야 합니다.  **Azure Portal** 에서 리소스를 제거하려면 **리소스 그룹** 을 클릭합니다.  랩에서 만든 리소스 그룹을 모두 선택합니다.  리소스 그룹 블레이드에서 **리소스 그룹 삭제** 를 클릭하고 리소스 그룹 이름을 입력한 다음 **삭제** 를 클릭합니다.  추가로 만든 리소스 그룹이 있으면 이 프로세스를 반복합니다. **리소스 그룹을 삭제하지 않으면 다른 랩에서 문제가 발생할 수 있습니다.** |
| --- |

**축하합니다**! 이 랩이 완료되었습니다.
