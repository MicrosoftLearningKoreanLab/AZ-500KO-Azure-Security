---
lab:
    title: 'LAB 08_리소스 매니저 잠금을 사용하여 Azure 리소스 보호'
    module: '모듈 01 - 계정과 접근 관리'
---

# 랩: 리소스 매니저 잠금을 사용하여 Azure 리소스 보호

**시나리오**

리소스 매니저 잠금 기능은 관리자가 Azure 리소스를 잠그고 리소스를 삭제하거나 변경하는 것을 방지할 수 있는 방법을 제공합니다. 이러한 잠금 장치는 RBAC(역할 기반 액세스 제어) 계층 외부에 위치하며, 적용 시 모든 사용자에게 자원에 대한 제한을 가합니다. 이는 사용자가 삭제하거나 변경할 수 없는 중요한 리소스를 구독에 가지고 있을 때 매우 유용하며 우발적이고 악의적인 변경 또는 삭제를 방지하는 데 도움이 될 수 있습니다.

적용할 수 있는 잠금에는 두 가지 유형이 있습니다.

 - **CanNotDelete** - 잠금이 적용되는 동안 다른 사람이 리소스를 삭제하는 것을 방지하지만, 리소스 변경은 가능합니다. 
 - **ReadOnly** - =이름에서 알 수 있듯이 자원을 읽기 전용으로 만들어 변경과 삭제를 할 수 없습니다. 필요에 따라 리소스 잠금을 구독, 리소스 그룹 또는 개별 리소스에 적용할 수 있습니다. 구독에 잠금을 적용하면 해당 구독의 모든 리소스(나중에 추가된 리소스 포함)에 동일한 잠금이 적용됩니다. 일단 적용되면, 이러한 잠금장치는 역할에 상관없이 모든 사용자에게 적용됩니다. 잠금 장치가 있는 리소스를 삭제하거나 변경할 필요가 있는 경우, 잠금 장치를 제거해야 합니다.

**권한**

잠금 설정 및 제거 권한을 사용하려면 다음 RBAC 권한 중 하나에 액세스해야 합니다.

- `Microsoft.Authorization/*`
- `Microsoft.Authorization/locks/*`

기본적으로 이러한 작업은 RBAC 역할에 내장된 소유자 및 사용자 액세스 관리자에서만 사용할 수 있지만 필요에 따라 사용자 지정 역할에 추가할 수 있습니다. 이러한 역할을 가진 사용자들도 잠금의 적용을 받지만, 필요시 잠금을 제거할 수 있습니다. 잠금 생성 및 삭제는 Azure Activity 로그에서 추적됩니다.


# 랩 7: 리소스 매니저 잠금으로 Azure 리소스 보호

잠금은 ARM 템플릿 내에서 리소스를 생성할 때 생성하거나, Azure 포털 또는 PowerShell을 사용하여 생성할 수 있습니다.


## 연습 1: 잠금 생성

잠금이 적절히 적용되어 리소스를 보호하도록하는 가장 좋은 방법은 런타임에 잠금을 생성하고 ARM 템플릿에 구성하는 것입니다. 잠금은 최상위 ARM 리소스이며 잠기는 자원에 해당하지 않습니다. 잠금을 적용할 리소스가 미리 존재해야 합니다. 


### 작업 1: 잠금 추가 (Azure 포털)

1.  Cloud Shell의 PowerShell 세션에 다음 명령을 입력하여 리소스 그룹과 스토리지 계정을 생성한다.  _(명령의 XXXXXX 부분을 고유한 문자로 대체한다)_

     ```powershell
     New-AzResourceGroup -Name LockRG -Location EastUS
     ```
    
     ```powershell
     New-AzStorageAccount -ResourceGroupName LockRG -Name XXXXXX -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
     ```

1.  스토리지 계정을 찾아 선택한다. 좌측 메뉴에서 **잠금**을 클릭한다.

     ![Screenshot](../Media/Module-1/1adf9f0b-8325-40ce-a763-94008b9c63ae.png)

1.  **추가**를 클릭한다.

1.  잠금 이름과 설명을 입력하고, 잠금 유형을 읽기 전용 혹은 삭제를 선택한다.

     ![Screenshot](../Media/Module-1/511e54e3-c876-454e-9a3d-e2896fcc990d.png)

1.  **확인**을 클릭하고, 잠금을 저장하여 리소스를 보호한다. 

1.  **잠금** 블레이드로 돌아가 잠금을 삭제한다.


### 작업 2: 잠금 추가 (PowerShell)

1.  Cloud Shell의 PowerShell 세션에 다음 명령을 입력하여 스토리지 계정에 대한 잠금을 생성한다. _(명령의 XXXXXX 부분을 스토리지 계정의 이름으로 대체한다)_

     ```powershell
     Connect-AzureAD
     ```

     ```powershell
     New-AzResourceLock -LockLevel CanNotDelete -LockName criticalStorageLock -ResourceName XXXXXX -ResourceType Microsoft.Storage/storageAccounts -ResourceGroupName LockRG
     ```

1.  다음 명령을 입력하여 잠금을 삭제한다. _(명령의 XXXXXX 부분을 스토리지 계정의 이름으로 대체한다)_

     ```powershell
     Remove-AzResourceLock -LockName criticalStorageLock -ResourceName  XXXXX -ResourceGroupName LockRG -ResourceType Microsoft.Storage/storageAccounts
     ```
     확인 메시지가 뜨면 **Y**를 입력하고 **Enter**를 누른다.

리소스 로그(Resource Logs)를 사용하면 중요한 리소스의 우발적이거나 악의적인 변경 및 삭제에 대한 추가 보호를 구현할 수 있습니다. 관리자가 여전히 이러한 잠금 장치를 제거할 수 있기 때문에 완벽하지는 않지만, 잠금 장치를 제거하는 유일한 목적은 이를 우회하는 것이기 때문에 의식적인 노력이 필요합니다. 이러한 잠금 장치는 RBAC 외부에 있으므로, 어떤 역할이나 사용자 지정 권한을 부여했는지에 관계없이 이를 적용하고 모든 사용자에게 영향을 미치고 있는지 확인해야 합니다.
