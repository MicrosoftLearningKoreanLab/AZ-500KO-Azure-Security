---
lab:
    title: 'LAB 03_Kubernetes 클러스터 만들기'
    module: '모듈 02 - 플랫폼 보호'
---

# 랩: Kubernetes 클러스터 만들기

AKS (Azure Kubernetes Service)는 클러스터를 신속하게 배포하고 관리 할 수 있는 관리형 Kubernetes 서비스입니다. 이 랩에서는 웹 프론트 엔드 및 Redis 인스턴스를 포함하는 다중 컨테이너 응용프로그램이 클러스터에서 실행됩니다. 그런 다음 응용 프로그램을 실행하는 클러스터 및 포드의 상태를 모니터링하는 방법을 알아봅니다.


### 연습 1: AKS 환경 만들기

#### 작업 1: AKS 클러스터 배포

1. <a href="https://portal.azure.com" target="_blank"><span style="color: #0066cc;" color="#0066cc">Azure Portal</span></a>에 로그인 합니다.
1.  **Cloudshell** 아이콘을 클릭한다.

     ![Screenshot](../Media/Module-2/4efbdec1-f1c9-4c37-8ca5-193f245a274d.png)

1.  **Azure CLI BASH**를 선택하고, 필요하다면 스토리지 계정을 생성한다. 

1.  다음 명령을 입력하여 리소스 그룹을 생성한다. 

    ```bash
    az group create --name myAKSResourceGroup --location eastus
    ```

     **참고**: 만약 ```Microsoft.Network``` 리소스 공급자가 등록되지 않았다는 에러 메시지가 표시되면, 다음 명령을 실행하고 다시 4번 명령을 입력한다. 그렇지 않으면 작업 2를 진행한다. 
     
    ```bash
    az provider register --namespace 'Microsoft.Network'
    ```


### 작업 2: CLI로 AKS 클러스터 배포

1.  **CloudShell**에 다음 명령을 실행한다. 

    ```bash
    az aks create --resource-group myAKSResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```
 
2.  몇 분 후 명령이 완료되고 클러스터에 대한 **JSON 형식** 정보를 반환한다.

> **참고**: AKS 클러스터가 배포되는데 약 10~15분 정도가 소요됩니다.


### 작업 3: 클러스터에 연결

Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트 인 kubectl을 사용해야 합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다.

1.  Azure Portal에서 **Cloud Shell**을 Bash 모드로 실행한다.

1.  다음 명령어를 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성한다. 해당 명령어는 자격 증명을 다운로드하고 **Kubernetes CLI**를 구성한다.

    ```azurecli-interactive
    az aks get-credentials --resource-group myAKSResourceGroup --name myAKSCluster
    ```

1.  다음 `kubectl` 명령어를 사용하여 Kubernetes 클러스터에 연결된 노드 목록을 확인한다.


    ```azurecli-interactive
    kubectl get nodes
    ```

1.  다음은 이전 단계에서 출력된 결과에 대한 예시다. 노드의 **STATUS**가 **Ready**인지 확인한다.

    ```json
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.9.11
    ```


#### 작업 4: 응용프로그램 실행

Kubernetes 매니페스트 파일은 실행할 컨테이너 이미지와 같은 원하는 클러스터 상태를 정의합니다. 이 랩에서는 매니페스트를 사용하여 Azure Vote 응용 프로그램을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 두 개의 kubernetes-deployment가 포함되어 있습니다. 하나는 샘플 Azure Vote Python 응용 프로그램 용이고 다른 하나는 Redis 인스턴스 용입니다. 두 개의 kubernetes-service도 작성됩니다. Redis 인스턴스의 내부 서비스와 인터넷에서 Azure Vote 애플리케이션에 액세스하는 외부 서비스입니다. 이 실습을 위해 매니페스트 파일이 생성되어 Github 페이지에 저장되었습니다. 파일은 azure-vote.yaml이며 **`https://raw.githubusercontent.com/MicrosoftLearning/AZ-500-Azure-Security/master/Allfiles/Labs/Mod2_Lab03/azure-vote.yaml`**에 있습니다.

1. Cloud Shell에서 다음 명령어를 실행하여 Kubernetes 매니페스트 파일인 yaml 파일을 참조하여 Pod를 배포한다.

     ```bash
    kubectl apply -f https://raw.githubusercontent.com/MicrosoftLearning/AZ-500-Azure-Security/master/Allfiles/Labs/Mod2_Lab03/azure-vote.yaml
    ```

2. 이전 단계의 명령어의 결과가 다음과 같다면 성공적으로 Pod가 생성되었음을 알 수 있다.

     ```json
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

> **참고**: 응용 프로그램이 실행될 때 Kubernetes 서비스는 응용프로그램의 프런트 엔드를 인터넷에 노출합니다. 이 프로세스는 완료하는 데 몇 분이 걸릴 수 있습니다.


#### 작업 5: 응용프로그램 테스트

1. 배포 진행상황을 모니터링 하려면 다음 명령어를 사용할 수 있다.

    ```azurecli-interactive
    kubectl get service azure-vote-front --watch
    ```

1.  *azure-vote-front* 서비스는 초기에 *EXTERNAL-IP*가 *pending*으로 출력된다.

    ```
    NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
    azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP    6s
    ```

1.  *EXTERNAL-IP* 주소가 *pending*에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 kubectl 감시 프로세스를 중지한다. 다음 출력 예시는 서비스에 할당된 유효한 공용 IP 주소를 보여준다.

    ```
    azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/    TCP   2m
    ```

1.  배포된 응용프로그램이 정상적으로 동작하는지 확인하려면 *EXTERNAL-IP*에 출력된 IP를 복사하여 새로운 웹 브라우저 주소창에 붙여넣어 탐색한다.

     ![Screenshot](../Media/Module-2/88d51dc5-a992-436f-a65e-83a766c142a9.png)


#### 작업 6: 상태와 로그 모니터링

AKS 클러스터가 생성되면 컨테이너용 Azure Monitor가 클러스터 노드와 Pod에 대한 상태 메트릭을 캡처 할 수있습니다. 이러한 상태 메트릭은 Azure Portal에서 사용할 수 있습니다.

Azure Vote Pod의 현재 상태, 가동 시간 및 리소스 사용량을 보려면 다음 단계를 완료하십시오.

1. <a href="https://portal.azure.com" target="_blank"><span style="color: #0066cc;" color="#0066cc">Azure Portal</span></a>에 접속한다.

1. 앞서 생성한 **myAKSCluster**를 탐색한다.

1. **모니터링**섹션에 있는 **Insights**를 클릭한다.

1. 상단의 **+ 필터 추가**를 클릭한다.

1. **Select property**에 **네임 스페이스**를 선택하고 **값 선택**에 **<kube-system을 제외한 모든 항목>**을 선택한다.

1. **컨테이너** 탭으로 이동하여 배포된 Pod를 확인한다.

    *azure-vote-back*과 *azure-vote-front* 컨테이너가 배포된 것을 확인할 수 있습니다.

1. `azure-vote-front` Pod의 로그를 보려면 컨테이너 목록의 오른쪽에서 **Analtyics에서 보기** 드롭다운 메뉴를 클릭한 후 **컨테이너 로그 보기**를 클릭합니다. 이 로그에는 컨테이너의 *stdout*과 *stderr* 스트림이 포함됩니다.


### 작업 7: 클러스터 삭제

클러스터가 더 이상 필요하지 않으면 **`az group delete`** 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거하십시오.

1. 다음 명령을 **Cloud Shell**의 Bash 세션에 입력하여 리소스 그룹을 제거한다. 

    ```bash
    az group delete --name myAKSResourceGroup --yes --no-wait
    ```

    **참고**: 리소스 그룹을 삭제하는 데 다소 시간이 걸릴 수 있습니다. `--no-wait` 옵션은 명령을 백그라운드에서 실행합니다.

> **결과**: 이 연습을 완료한 후 이 랩에서 사용된 리소스 그룹을 제거했습니다.
