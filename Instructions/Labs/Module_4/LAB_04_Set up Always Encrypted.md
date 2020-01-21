

# 모듈 4: Always Encrypted를 설정하여 보안 데이터 구현


**시나리오**

이 모듈에 포함된 태스크는 다음과 같습니다.

 - Azure 기밀 컴퓨팅
 - Azure Key Vault


## 연습 1: Azure Key Vault 소개


**시나리오**

이 랩에서는 Azure Key Vault를 사용하여 Azure에서 암호화 키와 비밀을 저장하고 관리하는 데 사용할 강화된 컨테이너(자격 증명 모음)를 만듭니다. 먼저 Azure PowerSell을 사용하여 컨테이너를 만든 다음 이 컨테이너에 암호를 저장합니다. 이 암호는 Azure 애플리케이션에서 비밀로 사용할 수 있습니다.

### 태스크 1: SQL Server Management Studio 다운로드

1.  이 랩에 필요한 최신 버전 SQL Management Studio를 다운로드하려면 해당 링크(**`https://docs.microsoft.com/ko-kr/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017`**)를 방문하여 SQL Management Studio 다운로드를 클릭하세요.



### 태스크 2: PowerShell을 사용하여 Key Vault 만들기


이 연습에서는 PowerShell을 사용하여 Azure Key Vault를 만듭니다.


1.  **시작 > PowerShell**을 클릭하여 PowerShell을 시작합니다.

2.  다음 명령을 사용하여 Azure 구독용 계정으로 Azure에 인증합니다.

     ```powershell
    Login-AzureRMAccount
     ```

4.  새 리소스 그룹을 만듭니다. 

     ```powershell
    New-AzureRmResourceGroup -Name 'KeyVaultPSRG' -Location 'eastus'
     ```


5.  리소스 그룹에 키 자격 증명 모음을 만듭니다. **VaultName은 고유하게 지정해야 합니다.**

     ```powershell
    New-AzureRmKeyVault -VaultName 'KeyVaultPS' -ResourceGroupName    'KeyVaultPSRG' -Location 'eastus'
     ```

    **참고**: 이 명령의 출력에는 자격 증명 모음 이름(여기서는 KeyVaultPS) 및 자격 증명 모음 URI(`https://KeyVaultPS.vault.azure.net`)와 같은 중요한 정보가 표시됩니다.



6.  Azure Portal에서 **KeyVaultPSRG** 리소스 그룹을 엽니다.

7.  KeyVaultPS를 클릭하여 생성한 자격 증명 모음을 확인합니다.

### 태스크 3: Key Vault에 키와 비밀 추가

1.  PowerShell 창으로 돌아옵니다.

2.  아래 명령을 사용하여 Key Vault에 소프트웨어 보호 키를 추가합니다. 자리 표시자 텍스트는 자격 증명 이름으로 변경하세요.

     ```powershell
    $key = Add-AzureKeyVaultKey -VaultName '<YourVaultName>' -Name 'MyLabKey'     -Destination 'Software'
     ```

3.  Azure Portal에서 **KeyVaultPS**로 다시 이동하여 왼쪽 탐색 영역의 설정에서 키를 클릭합니다.

4.  **MyLabKey**를 클릭합니다.

5.  현재 버전을 클릭합니다.

6.  생성한 키 관련 정보를 점검합니다.

    **참고**: URI를 사용하여 언제든지 이 키를 참조할 수 있습니다. 최신 버전을 가져오려는 경우 `https://keyvaultps.vault.azure.net/keys/MyLabKey/`만 참조하면 됩니다. 이 URI의 정확한 버전은 `https://keyvaultps.vault.azure.net/keys/MyLabKey/da1a3a1efa5dxxxxxxxxxxxxxd53c5959e`입니다.


7.  PowerShell 창으로 다시 이동합니다. 키의 현재 버전을 표시하려면 다음 명령을 입력합니다.

     ```powershell
    $Key.key.kid
     ```


8.  방금 만든 키를 확인하려는 경우 Get-AzureKeyVaultKey cmdlet을 사용하면 됩니다. 자리 표시자 텍스트는 자격 증명 이름으로 변경하세요.

     ```powershell
    Get-AzureKeyVaultKey -VaultName '<YourVaultName>'
     ```


### 태스크 4: Key Vault에 비밀 추가

1.  다음으로는 **KeyVaultPS**에 비밀을 추가합니다. 이렇게 하려면 다음 코드를 사용하여 **$secretvalue** 변수를 추가합니다.

     ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
     ```


2.  그런 후에 다음 명령을 사용하여 자격 증명 모음에 비밀을 추가합니다. 자리 표시자 텍스트는 자격 증명 이름으로 변경하세요.

     ```powershell
    $secret = Set-AzureKeyVaultSecret -VaultName '<YourVaultName>' -Name      'SQLPassword' -SecretValue $secretvalue
     ```

3.  Azure Portal의 **KeyVaultPS**로 다시 이동하여 **비밀**을 클릭합니다.

4.  **SQLPassword** 비밀을 클릭합니다.

5.  현재 버전을 클릭합니다.

6.  생성한 비밀을 검사합니다.

    **참고**: URI를 사용하여 언제든지 이 키를 참조할 수 있습니다. 최신 버전을 가져오려는 경우 `https://keyvaultps.vault.azure.net/secrets/SQLPassword`만 참조하면 됩니다. 이 URI의 정확한 버전은 `https://keyvaultps.vault.azure.net/secrets/SQLPassword/c5aada85d3acxxxxxxxxxxe8701efafcf3`입니다.


7.  **비밀 값 표시** 단추를 클릭하고 암호가 표시되는지 확인합니다.

8.  비밀을 확인하려는 경우 Get-AzureKeyVaultSecret cmdlet을 사용합니다. 자리 표시자 텍스트는 자격 증명 이름으로 변경하세요.

     ```powershell
    Get-AzureKeyVaultSecret -VaultName '<YourVaultName>'
     ```

### 태스크 5: 클라이언트 애플리케이션을 사용하도록 설정


이 태스크에서는 클라이언트 애플리케이션이 Azure SQL Database 서비스에 액세스할 수 있도록 설정합니다. 이렇게 하려면 필요한 인증을 설정하고, 애플리케이션을 인증하는 데 필요한 애플리케이션 ID 및 비밀을 가져옵니다. 다음 PowerShell 코드를 사용하여 이러한 단계를 수행합니다.


1.  Azure Portal을 열고 Azure Active Directory로 이동합니다.

2.  왼쪽 탐색 영역의 관리에서 앱 등록을 클릭합니다.

3.  **+새 앱 등록을 클릭합니다.**

1.  애플리케이션 이름으로 **sqlApp**을 입력하고 **웹앱 API**를 선택한 다음 로그온 URL로 **`http://sqlapp`**을 입력합니다.

5.  **만들기**를 클릭합니다.

6.  앱 등록이 완료된 후 **sqlApp**이 자동으로 표시되지 않으면 클릭합니다.

7.  나중에 필요하므로 애플리케이션 ID를 복사합니다.

8.  **설정**을 클릭합니다.

9.  **키**를 클릭합니다.

10.  키 섹션에서 설명에 **Key1**을 입력합니다. 만료 드롭다운 목록에서 **1년**을 선택합니다.


1.  키 값을 값 상자에 붙여넣고 저장을 클릭합니다. 블레이드를 닫습니다. 블레이드를 다시 열면 값이 숨겨진 것으로 표시됩니다.

### 태스크 6: 애플리케이션의 Key Vault 액세스를 허용하는 Key Vault 정책 추가

1.  **Azure Portal**에서 랩 첫부분에서 만든 **리소스 그룹**을 엽니다.

1.  **Azure Key Vault**를 선택합니다.

1.  **액세스 정책**을 클릭합니다.

1.  Azure 구독과 연결된 계정을 선택합니다.

1.  **키 권한** 드롭다운에서 **모두 선택**을 선택하여 모든 권한을 강조 표시합니다.

1.  확인을 선택합니다.

1.  사용자 목록으로 돌아와 **저장**을 클릭합니다.

    **중요**! 저장을 클릭하지 않으면 권한이 커밋되지 않습니다. 


8.  **Powershell ISE**에서 다음 PowerShell을 실행하여 sqlApp 키 권한을 설정합니다. 여기서 자리 표시자 텍스트는 **사용자의 계정 세부 정보**로 바꿉니다.

    
     ```powershell
    $subscriptionName = '[Azure_Subscription_Name]'
    $applicationId = '[Azure_AD_Application_ID]'
    $resourceGroupName = '[Resource_Group_with_KeyVault]'
    $location = '[Azure_Region_of_KeyVault]'
    $vaultName = '[KeyVault_Name]' 
     ```
    
     ```powershell
    Login-AzureRmAccount
     ```
    
     ```powershell
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName `-ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
     ```


 
### 태스크 7: Key Vault를 사용하여 Azure SQL Database를 통해 데이터 암호화


**시나리오**

이 태스크에서는 빈 Azure SQL Database를 만들어 SQL Management Studio에 연결한 다음 테이블을 만듭니다. 그런 다음 Azure Key Vault에서 자동 생성된 키를 사용하여 데이터 열 두 개를 암호화합니다. 그 후에는 Visual Studio를 사용해 콘솔 애플리케이션을 만들어서 암호화된 열에 데이터를 로드하고, Key Vault를 통해 키에 액세스하는 연결 문자열을 사용하여 해당 데이터에 안전하게 액세스합니다.


1.  Azure Portal에서 **리소스 만들기> 데이터베이스> SQL Database**를 클릭합니다.

2.  SQL Database 블레이드에 다음 세부 정보를 입력하고 만들기를 클릭합니다.

      - 데이터베이스 이름: **medical**

      - 리소스 그룹: (새로 만들기) **SQLEncryptRG**

      - 원본 선택: **빈 데이터베이스**

      - 서버 **설정 구성 필요** : **새 서버 만들기**

          - 서버 이름: **[고유한 서버 이름]**

          - 서버 관리자 로그인: **demouser**

          - 암호: **Pa55w.rd1234**

          - 위치: **[KeyVaultPS와 같은 위치]**

          - **선택**을 클릭합니다.

      - 가격 책정 계층: S0



1.  위의 모든 항목을 구성한 후 **만들기**를 선택합니다.

3.  SQL Database가 배포되면 Azure Portal에서 SQL Database를 열고 연결 문자열을 찾아서 복사합니다.
경고
**참고**: 나중에 사용할 수 있도록 연결 문자열을 저장할 때는 {your_username}을 **demouser**로, {your_password}는 **Pa55w.rd1234**로 바꾸세요.


### 태스크 8: SQL Database에서 테이블 만들기

1.  Azure Portal을 사용하여 Medical 데이터베이스가 있는 서버 이름을 찾아서 복사합니다.



2.  같은 블레이드에서 서버 방화벽 설정을 클릭합니다.



3.  그런 다음 **+클라이언트 IP 추가**, **저장**을 차례로 클릭합니다.



4.  LABVM에서 SQL Server Management Studio를 엽니다. 서버에 연결 대화 상자에서 다음 속성을 사용하여 서버에 연결합니다.

  - 서버 유형: **데이터베이스 엔진**

  - 서버 이름: **[데이터베이스 개요 블레이드에 있음]**

  - 인증: **SQL Server 인증**

  - 로그인: **demouser**

  - 암호: **Pa55w.rd1234**


### 태스크 9: 테이블 만들기 및 암호화

1.  SQL Management Studio에서 **데이터베이스**를 확장하고 medical을 마우스 오른쪽 단추로 클릭한 다음 새 쿼리를 선택합니다.

2.  다음 코드를 쿼리 창에 붙여넣고 실행을 클릭합니다.

     ```sql
    CREATE TABLE [dbo].[Patients](
    
        [PatientId] [int] IDENTITY(1,1),

        [SSN] [char](11) NOT NULL,

        [FirstName] [nvarchar](50) NULL,

        [LastName] [nvarchar](50) NULL,

        [MiddleName] [nvarchar](50) NULL,

        [StreetAddress] [nvarchar](50) NULL,

        [City] [nvarchar](50) NULL,

        [ZipCode] [char](5) NULL,

        [State] [char](2) NULL,

        [BirthDate] [date] NOT NULL 

        PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );


     ```


3.  테이블이 정상적으로 생성되면 **medical > 테이블**을 확장하고 dbo.Patients를 마우스 오른쪽 단추로 클릭한 다음 **열 암호화**를 선택합니다.



4.  다음을 클릭합니다.

5.  열 선택 화면에서 **SSN** 및 **Birthdate**를 선택합니다. 그런 다음 SSN의 암호화 종류는 **결정적**으로, Birthdate의 암호화 종류는 **임의**로 설정합니다. **다음**을 클릭합니다.



6.  마스터 키 구성 페이지의 키 저장소 공급자 선택에서 **Azure Key Vault**를 클릭합니다. **로그인**을 클릭하고 인증합니다. Azure Key Vault를 선택합니다. **다음**을 클릭합니다.



7.  실행 설정 화면에서 **다음**, **마침**을 차례로 클릭하여 암호화를 진행합니다.



8.  **medical > 보안> Always Encrypted 키**를 확장하여 이제 키가 표시됨을 확인합니다.



### 태스크 10: 암호화된 열 사용을 위한 콘솔 애플리케이션 빌드

1.  LABVM에서 Visual Studio 2019를 열고 로그인합니다.

2.  **파일 > 새로 만들기 > 프로젝트**를 클릭합니다.

3.  그런 다음 **Visual C# > 콘솔 앱(.NET Framework)**를 선택하고 **C:** 위치에 이름으로 **OpsEncrypt**를 입력한 다음 **확인**을 클릭합니다.


4.  **OpsEncrypt**를 프로젝트 **마우스 오른쪽 단추으로 클릭**하고 **속성**을 클릭합니다.



5.  **대상 프레임워크**를 **.NET Framework 4.6.2**로 변경하고 **대상 프레임워크**를 변경하라는 메시지가 표시되면 **예**를 클릭합니다.



6.  **NuGet 패키지 관리자 콘솔**을 **엽니다.**



7.  **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**로 이동하여 다음 **NuGet** 패키지를 설치합니다.

     ```powershell
    Install-Package     Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
     ```

     ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
     ```

1.  브라우저를 열고 **`https://aka.gd/az300t03mod5code`**로 이동하여 코드를 복사합니다.

8.  **Program.cs**의 코드를 방금 복사한 코드로 바꿉니다.

9.  Main 메서드에서 연결 문자열 설정을 찾은 다음 이전 단계에서 복사한 값으로 바꿉니다.

10.  **Visual Studio**에서 **시작 단추**를 **클릭**합니다.


1.  **콘솔 애플리케이션**이 **빌드**된 다음 시작됩니다. 먼저 애플리케이션에 데이터가 추가된 후 암호를 입력하라는 메시지가 표시됩니다.

    - 서버 암호: **Pa55w.rd1234**

1.  **콘솔 애플리케이션**은 실행 중인 상태로 **유지**하고 **SQL Management Studio**로 이동합니다. medical 데이터베이스를 마우스 오른쪽 단추으로 클릭하고 **새 쿼리**를 클릭합니다.



1.  다음 쿼리를 실행하여 데이터베이스에 로드된 데이터가 암호화되었는지 확인합니다.

     ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
     ```


1.  이제 콘솔 애플리케이션으로 다시 이동하면 **유효한 SSN**을 **입력**하라는 메시지가 표시됩니다. 이 SSN을 입력하면 암호화된 열에서 데이터를 쿼리합니다. 그러면 Key Vault에서 키가 호출되어 암호화가 해제된 데이터가 콘솔 창에 표시됩니다.

     ```sql
    999-99-0003
     ```

1.  Enter 키를 눌러 명령을 **끝냅니다**.


| 경고: 계속하기 전에 이 랩에서 사용한 모든 리소스를 제거해야 합니다.  **Azure Portal**에서 리소스를 제거하려면 **리소스 그룹**을 클릭합니다.  랩에서 만든 리소스 그룹을 모두 선택합니다.  리소스 그룹 블레이드에서 **리소스 그룹 삭제**를 클릭하고 리소스 그룹 이름을 입력한 다음 **삭제**를 클릭합니다.  추가로 만든 리소스 그룹이 있으면 이 프로세스를 반복합니다. **리소스 그룹을 삭제하지 않으면 다른 랩에서 문제가 발생할 수 있습니다.** |
| --- |

**결과**: 이 과정을 완료했습니다.


