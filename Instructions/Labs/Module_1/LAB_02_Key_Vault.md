# 모듈 1: 랩 2 - Key Vault (항상 암호화되도록 설정하여 보안 데이터 구현)


**시나리오**

 이 모듈은 다음 작업을 포함합니다.

 - Azure confidential computing
 - Azure Key Vault


## 연습 1: Azure Key Vault 소개

**시나리오**

이 랩에서는 Azure Key Vault로 Azure에 강화된 컨테이너(볼트)를 만들고, Azure에 암호키와 비밀을 저장하고 관리합니다. Azure PowerShell을 사용하여 암호를 비밀(Secret)로 저장하고 Azure 애플리케이션과 함께 사용합니다.


### 작업 1: SQL Server Management Studio 다운로드

1.  이 실습에 필요한 SQL Management Studio의 최신 버전을 다운로드하기 위해 다음 링크에서 **Download SQL Server Management Studio**를 선택한다. **`https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017`**

     **참고:** SQL Management Studio가 설치될 때까지 기다리지 않고 다음 작업을 진행하십시오. 


### 작업 2: PowerShell을 이용해 Key Vault 생성

이 작업에서는 PowerShell을 이용하여 Azure Key Vault를 생성합니다.

1.  **시작 > PowerShell** 을 클릭하여 PowerShell을 시작한다.

2.  다음 명령을 입력하여 구독이 있는 Azure 계정을 인증한다. 

    ```powershell
    Login-AzAccount
    ```

4.  리소스 그룹을 생성한다.

    ```powershell
    New-AzResourceGroup -Name 'KeyVaultPSRG' -Location 'eastus'
    ```

5.  리소스 그룹에 키 볼트를 생성한다. VaultName은 고유해야 하므로 <keyvault name>을 고유한 이름으로 변경한다.

    ```powershell
    New-AzKeyVault -VaultName '<keyvault name>' -ResourceGroupName 'KeyVaultPSRG' -Location 'eastus'
    ```
    **참고**: 명령의 출력은 중요한 정보를 담고 있습니다. 예를 들어 Vault 이름이 KeyVaultPS인 경우 Vault URI는 다음과 같습니다 : `https://KeyVaultPS.vault.azure.net`

6.  Azure 포털에서 **KeyVaultPSRG** 리소스 그룹을 클릭한다.

7.  키 볼트 이름을 클릭한다.

    **참고**: 이후 등장하는 모든 키 볼트 이름 **KeyVaultPS**를 생성한 Key Vault의 이름으로 대체하여 실습을 진행하십시오.

8.  **Access 정책** > **+ 액세스 정책 추가** 를 클릭한다.

9.  **템플릿에서 구성(선택 사항)** 으로부터 **키, 비밀 및 인증서 관리**를 선택한다. 

10. **주체 선택**을 클릭하고 실습자의 계정을 할당한 후, **선택**을 클릭한다.

11. **추가**를 클릭하고 **저장**한다.


### 작업 3: 키 볼트에 키와 비밀(secret) 추가

1.  PowerShell 창으로 돌아간다.

2.  다음 명령을 사용하여 키 볼트에 소프트웨어로 보호된 키를 추가한다. ('YourVaultName'을 생성한 볼트의 이름으로 변경한다.)

    ```powershell
    $key = Add-AZKeyVaultKey -VaultName '<YourVaultName>' -Name 'MyLabKey' -Destination 'Software'
    ```

3.  Azure 포털의 **KeyVaultPS** 페이지로 돌아가 설정 섹션의 **키**를 클릭한다. 

4.  **MyLabKey**를 클릭한다.

5.  현재 버전을 클릭한다.

6.  생성한 키에 대한 정보를 확인한다. 

    **참고**: URI를 사용하면 이 키를 항상 참조할 수 있습니다. 최신 버전을 가져오려면 `https://keyvaultps.vault.azure.net/keys/MyLabKey/`를 참조하십시오. 정확한 버전이 필요하면 다음을 참조하십시오. `https://keyvaultps.vault.azure.net/keys/MyLabKey/da1a3a1efa5dxxxxxxxxxxxxxd53c5959e`

7.  PowerShell 창으로 돌아가서 다음 명령을 입력하여 키의 현재 버전을 확인한다. 

    ```powershell
    $Key.key.kid
    ```

8.  방금 생성한 키를 확인하려면 Get-AzureKeyVaultKey cmdlet을 사용한다. ('YourVaultName'을 생성한 볼트의 이름으로 변경한다.)

    ```powershell
    Get-AZKeyVaultKey -VaultName '<YourVaultName>'
    ```


### 작업 4: 키 볼트에 비밀(secret) 추가

1.  **KeyVaultPS**에 비밀을 추가하기 위해 다음 명령을 사용하여 **$secretvalue** 변수를 추가한다.

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

2.  다음 명령을 사용하여 볼트에 비밀을 추가한다. ('YourVaultName'을 생성한 볼트의 이름으로 변경한다.)

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName '<YourVaultName>' -Name 'SQLPassword' -SecretValue $secretvalue
    ```

3.  Azure 포털의 **KeyVaultPS** 창으로 돌아가 **비밀**을 클릭한다.

4.  **SQLPassword**를 비밀을 클릭한다.

5.  현재 버전을 클릭한다.

6.  생성한 비밀을 검토한다. 

    **참고**: URI를 사용하면 이 키를 항상 참조할 수 있습니다. 최신 버전을 가져오려면 `https://keyvaultps.vault.azure.net/secrets/SQLPassword` 를 참조하십시오. 정확한 버전이 필요하면 다음을 참조하십시오. `https://keyvaultps.vault.azure.net/secrets/SQLPassword/c5aada85d3acxxxxxxxxxxe8701efafcf3`

7.  **비밀 값 표시** 버튼을 클릭한다. (비밀번호가 나타난다.)

8.  비밀을 확인하기 위해서는 Get-AzureKeyVaultSecret cmdlet를 사용한다. ('YourVaultName'을 생성한 볼트의 이름으로 변경한다.)

    ```powershell
    Get-AZKeyVaultSecret -VaultName 'YourVaultName'
    ```


### 작업 5: 클라이언트 응용 프로그램 사용

클라이언트 응용 프로그램이 Azure SQL 데이터베이스 서비스에 액세스할 수 있도록 설정하십시오. 이 작업은 필요한 인증을 설정하고 응용프로그램을 인증하는 데 필요한 응용 프로그램 ID 및 암호를 취득하여 수행됩니다. 이 단계는 Azure 포털에서 수행합니다.

1.  Azure 포털에서 Azure Active Directory를 클릭한다.

2.  **관리** 섹션의 **앱 등록**을 클릭한다.

3.  **+ 새 등록**을 클릭한다.

1.  애플리케이션 이름으로 **sqlApp**을 입력한다. **리디렉션 URI(선택사항)** 아래 **웹**을 선택하고, **`https://sqlapp`** 을 입력한다.

5.  **등록**을 클릭한다.

6.  앱 등록이 완료되고 자동으로 나타나지 않으면 **sqlApp**을 클릭하십시오

7.  애플리케이션(클라이언트) ID를 복사해둔다.

8.  **인증서 및 암호**를 클릭한다.

9.  **+ 새 클라이언트 암호**를 클릭한다.

10. **설명** 섹션에 **Key1**을 입력하고 **만료 시간**을 **1년 후**로 설정한 후, **추가**를 클릭힌다. 

1.  Key1 값을 복사해둔다. 블레이드를 닫고 다시 열면 값이 숨김으로 표시된다. 


### 작업 6: 애플리케이션이 키 볼트에 액세스할 수 있도록 키 볼트 정책 추가

1.  **Azure 포털** 에서 랩 초반에 만들었던 **리소스 그룹**을 클릭한다. 

1.  앞서 생성한 키 볼트를 클릭한다.

1.  **액세스 정책**을 클릭한다.

1.  Azure 구독과 관련된 계정을 선택한다. 

1.  **키 권한** 드롭다운 메뉴에서 **모두 선택**을 클릭하여 모든 권한을 선택한다.

1.  **저장**을 클릭한다.

    **중요**! **저장**을 클릭해야 합니다. 그렇지 않으면 권한이 반영되지 않습니다.

8.  **Powershell ISE**에서 다음 Powershell 명령을 실행한다. sqlApp 키 권한을 설정하려면 각 자리 표시자 텍스트를 **사용자 계정 세부 정보**로 대체한다. 

    ```powershell
    $subscriptionName = '[Azure_Subscription_Name]'
    $applicationId = '[Azure_AD_Application_ID]'
    $resourceGroupName = '[Resource_Group_with_KeyVault]'
    $location = '[Azure_Region_of_KeyVault]'
    $vaultName = '[KeyVault_Name]' 
    ```

    ```powershell
    Login-AzAccount
    ```
    
    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

 
### 작업 7: 키 볼트를 사용하여 Azure SQL 데이터베이스로 데이터 암호화

**시나리오**

이 작업에서는 빈 Azure SQL 데이터베이스를 생성하고 SQL Server Management Studio로 연결하여 테이블을 생성합니다. 그런 다음 Azure Key Vault에서 자동 생성된 키를 사용하여 두 개의 데이터 열을 암호화합니다. Visual Studio를 사용하여 데이터를 암호화된 열에 로드한 다음, 키 볼트를 통해 키에 액세스하는 연결 문자열을 사용하여 해당 데이터에 안전하게 액세스합니다.

1.  Azure 포털에서 **+ 리소스 만들기 > Databases > SQL Database**를 클릭한다.

2.  SQL 데이터베이스 블레이드에서 다음 설정을 입력한 뒤, **검토 + 만들기**를 클릭한다. 

      - 리소스 그룹: (새로 만들기) **SQLEncryptRG**
      
      - 데이터베이스 이름: **medical**

      - 서버: **새로 만들기**

          - 서버 이름: **고유한 이름**

          - 서버 관리자 로그인: **demouser**

          - 암호: **Pa55w.rd1234**

          - 위치: **[KeyVaultPS와 같은 지역]**

          - **확인** 클릭

      - 컴퓨팅 + 스토리지 : 표준 S0

1.  설정을 마무리하면 **검토 + 만들기,** 와 **만들기**를 차례로 클릭한다.

3.  SQL Database가 배포되면 블레이드로 이동한 후, **데이터베이스 연결 문자열 표시**를 클릭하여 **ADO.NET**의 연결 문자열을 복사한다.

**참고**: 문자열을 사용할 때는 {your_username}을 **demouser**로, {your_password}를 **Pa55w.rd1234**로 대체하십시오.


### 작업 8: SQL 데이터베이스에 테이블 생성

1.  Azure 포털을 사용하여 Medical 데이터베이스가 위치한 서버 이름을 복사하고 클릭한다.

2.  **방화벽 설정 표시**를 클릭한다.

3.  **+ 클라이언트 IP 추가**를 클릭하고 **저장**을 클릭한다.

4.  SQL Server Management Studio를 실행하고, **Connect to Server** 대화 상자에 다음 속성을 사용하여 서버에 연결한다.

    - Server Type: **Database Engine**

    - Server Name: **[데이터베이스 개요 블레이드에서 확인한 서버 이름]**

    - Authentication: **SQL Server Authentication**

    - Login: **demouser**

    - Password: **Pa55w.rd1234**


### 작업 9: 테이블 생성 및 암호화

1.  SQL Server Management Studio에서 **Databases > medical 우클릭> New Query**를 실행한다.

2.  Query 창에 다음 코드를 복사 붙여넣기 하고 실행(Execute)한다.

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

3.  테이블 생성에 성공하면 **medical > tables > dbo.Patients** 을 우클릭하여 **Encrypt Columns**을 선택한다. 

4.  **Next**를 클릭한다.

5.  Column Selection 창에서 **SSN** 과 **Birthdate**를 체크한다. SSN의 Encryption Type을 **Deterministic**로, Birthdate의 Encryption Type을 **Randomized**로 설정하고 **Next**를 클릭한다.

6.  Master Key Configuration 페이지에서 Select the Key store provider를 **Azure Key Vault.** 로 설정하고 **Sign in**을 클릭하여 인증한다. 앞서 생성한 Azure 키 볼트를 선택하고, **Next**를 클릭한다.

7.  Run Settings 창에서 **Next**를 클릭한다. **Finish**를 클릭하여 암호화를 진행한다. 

8.  암호화 과정이 끝나면 **Close**를 클릭하고 **medical > security > Always Encrypted Keys** 메뉴를 확장하여 키를 확인한다. 


### 작업 10: 암호화된 열로 작업할 콘솔 응용 프로그램 빌드

1.  Visual Studio 2019를 실행하여 Azure 계정으로 로그인한다.

2.  **File > New > Project**를 클릭한다.

3.  **C# > 콘솔 앱 (.NET Framework)** 를 선택하고 프로젝트 이름을 **OpsEncrypt**로, 위치를 **C:\\** 로 지정하여 **만들기**를 클릭한다.

4.  **OpsEncrypt** 프로젝트를 우클릭하여 **속성**을 클릭한다.

5.  **대상 프레임워크**를 **.NET Framework 4.7.2.** 로 바꾸고, **대상 프레임워크 변경** 창이 뜨면 **예**를 클릭한다.

7.  **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**에서 다음 명령을 입력하여 **NuGet** 패키지를 설치한다.

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1.  Allfiles\\Labs\\Mod1_Lab02 의 **program.cs** 파일을 열어 코드를 복사한다. 

8.  **Program.cs** in Visual Studio의 **Program.cs** 내용을 방금 복사한 내용으로 대체한다.

9.  코드에서 **Connection string, clientId, and clientSecret** 를 찾아 이전 단계에서 복사한 값으로 대체한다. 

10.  **Visual Studio**의 시작 버튼을 클릭한다.

1.  **The Console Application** 은 **빌드**되고 시작된다. 앱은 비밀번호를 요구하고, 데이터베이스에 데이터를 추가한다.

    - Server Password: **Pa55w.rd1234**

1.  **콘솔 애플리케이션 실행**을 그대로 두고 **SQL Management Studio** 로 이동한다. medical 데이터베이스를 마우스 오른쪽 버튼으로 누르고 **New Query**를 클릭한다.

1.  다음 쿼리를 실행하여 데이터베이스에 로드된 데이터가 암호화되었는지 확인한다.

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

1.  이제 콘솔 응용 프로그램으로 돌아가 **Enter** **Valid SSN**을(를) 요청한다. 이것은 데이터에 대해 암호화된 열을 쿼리한다. Key Vault에서 호출된 키를 사용하면 이제 데이터가 암호화되지 않고 콘솔 창에 표시된다.

    ```sql
    999-99-0003
    ```

1.  **종료**하려면 enter를 누른다.

| 경고: 계속하기 전에 이 랩에 사용된 모든 리소스를 제거하십시오. **Azure 포털**에서 이 작업을 수행하려면 **리소스 그룹**을 클릭하십시오. 생성한 리소스 그룹을 선택하고, **리소스 그룹 삭제**를 누른 후 리소스 그룹 이름을 입력하여 **삭제**를 누르십시오. 생성한 추가 리소스 그룹에 대해 이 과정을 반복하십시오. **이 작업을 수행하지 않을 경우 다른 랩에 문제가 발생할 수 있습니다.**|
| --- |



