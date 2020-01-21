

# 모듈 4: 감사 로그 및 보고서 분석

## 연습 1: SQL 데이터베이스 감사 시작

### 태스크 0: 랩 설정

1.  **PowerShell** 을 열고 다음 명령을 실행하여 랩용 데이터베이스를 배포합니다.

     ```powershell
    start "https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoftLearning%2FAZ-500-Azure-Security%2Fmaster%2FAllfiles%2FLabs%2FMod4_Lab03%2Fazuredeploy.json" 
     ```

1.  **리소스 그룹** 아래에서 새로 만들기를 클릭하고 기본 이름인 "**Mod4Lab3**"을 사용하여 리소스 그룹을 만듭니다.

1.  입력되어 있는 기본 SQL Server 이름을 사용해도 됩니다.

1.  **구매** 를 클릭합니다. 

### 태스크 1 - 데이터베이스에 대해 감사 설정

1.  **리소스 그룹** 으로 **이동** 합니다.

1.  위에서 만든 리소스 그룹을 **선택** 합니다. 지침과 같은 이름을 선택한 경우 이 리소스 그룹은 "**Mod3Lab3**"입니다.

1.  **SQL Server** 이름을 **클릭** 합니다.

1.  왼쪽 창에서 감사를 클릭합니다.

1.  감사를 **끄기** 에서 **켜기** 로 **변경** 합니다.
경고
**참고**: 감사 로그를 작성할 위치를 선택하는 옵션이 표시됩니다.


1.  **3개 옵션**(**스토리지**, **Log Analytics**, **이벤트 허브**)을 모두 **선택** 합니다.

1.  **스토리지** 에서 구성을 **클릭** 합니다.

1.  **구독** 을 클릭하고 사용자의 구독을 선택합니다.

1.  스토리지 계정을 **클릭** 합니다.

1.  스토리지 계정 만들기에서 고유한 이름(**예: Mod4Lab3YOURNAME**)을 입력합니다.

1.  **확인을 클릭** 합니다.

1.  유효성 검사가 완료되면 **확인** 을 다시 클릭합니다.

1.  Log Analytics에서 구성을 클릭합니다.

1.  새 작업 영역 만들기를 **클릭** 합니다.

1.  다음 설정을 입력합니다.

     |Log Analytics 작업 영역|구독|리소스 그룹 | 위치| 가격 책정   계층|
     |-----------------------|------------|---------------|---------|   -------------
     |Mod4Lab3YOURNAME|사용자의 구독|랩 설정에서 만든 RG|   미국 동부 | GB당(2018)|

1.  **확인을 클릭** 합니다.
경고
**참고**: Azure Portal에서는 이 위치에서 이벤트 허브를 만들 수 없으므로, 이벤트 허브를 설정하려면 추가 단계를 수행해야 합니다.


1.  구성용 **이벤트 허브** 를 설정하려면 **Portal** 위쪽의 **Azure Cloud Shell** 을 클릭합니다.

1.  다음 **PowerShell 명령** 을 **입력** 합니다.

     ```powershell
    New-AzEventHubNamespace -ResourceGroupName Mod4Lab3  -NamespaceName     Mod3Lab4 -Location eastus
     ```

     ```powershell
    New-AzEventHub -ResourceGroupName Mod4Lab3 -NamespaceName Mod3Lab4  -EventHubName Mod3Lab4 -MessageRetentionInDays 3
     ```

1.  이러한 명령의 실행이 완료되면 Event Hubs 아래의 구성을 클릭합니다.

1.  다음 정보를 **선택** 합니다.

     | 구독|허브 네임스페이스|허브 이름| 허브 정책 이름|
     |------------|------------|--------|----------------|
     |사용자의 구독| Mod3Lab4|mod3lab4|RootManageSharedAccessKey|


1.  **확인을 클릭합니다.**

1.  이제 **감사 설정** 페이지에서 **저장** 을 클릭해도 됩니다.

    **결과**: SQL 데이터베이스에 대해 감사를 설정했습니다. 


1.  로그에 액세스하려면 **SQL Database** 및 **Server가 있는** **리소스 그룹** 으로 돌아옵니다.

1.  앞에서 만든 **Mod3Lab3** Log analytics 작업 영역을 **선택** 합니다.

1.  로그를 **클릭** 합니다.

1.  쿼리 공간에 다음 코드를 입력하고 **실행을 클릭** 합니다.

     ```cli
    Event | where Source  == "MSSQLSERVER" 
     ```

1.  결과가 표시되지 않습니다. 아래 경고의 내용을 확인하세요.
경고
**참고**: 테스트 데이터를 사용하여 새 데이터베이스에서 로그를 설정했으므로 최소한의 로그만 확인할 수 있습니다. 예제에서 로그가 표시되는 방식을 확인하려는 경우 확인 가능한 예제 데이터가 입력되어 있는 예제 Log Analytics 웹 사이트를 사용할 수 있습니다.


### 태스크 2 - 감사 로그 및 보고서 분석

1.  새 웹 브라우저 탭에서 **`https://portal.loganalytics.io/demo`** 로 이동합니다.

1.  쿼리 공간에 다음 코드를 입력하고 **실행을 클릭** 합니다.

     ```cli
    Event | where Source  == "MSSQLSERVER" 
     ```

1.  여기서 예제 감사 로그 몇 개를 확장하여 라이브 시스템에서 로그가 표시되는 방식을 확인할 수 있습니다.

1.  쿼리 공간에 다음 코드를 입력하고 **실행을 클릭** 합니다.

     ```cli
    Event 
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1d) 
    | where Source != "HealthService" 
    | where Source != "Microsoft-Windows-DistributedCOM" 
    | summarize count() by Source
     ```

1.  **차트를 클릭합니다.**

1.  여기서 로그 데이터를 차트 데이터로 표시하는 방법을 검토할 수 있습니다.

1.  **누적 세로 막대형** 드롭다운을 **클릭** 하고 **원형** 을 선택합니다.

1.  여기서 다른 차트와 같은 데이터를 확인할 수 있습니다.

1.  창의 오른쪽 위에서 **내보내기를 클릭** 하여 데이터를 **CSV** 로 내보낼 수 있습니다.

 Log Analytics에 사용되는 쿼리 언어는 Kusto 쿼리 언어입니다. 이 언어의 전체 설명서는 **`https://docs.microsoft.com/ko-kr/azure/kusto/query/`** 에서 확인할 수 있습니다.



**결과**: SQL 데이터베이스에서 로그를 설정하고, Log Analytics 작업 영역을 대상으로 쿼리를 실행하여 생성되는 감사 로그를 확인하는 작업을 완료했습니다.


