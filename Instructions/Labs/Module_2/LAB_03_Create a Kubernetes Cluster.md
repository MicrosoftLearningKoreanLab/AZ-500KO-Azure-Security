---
lab:
    title: '랩 3 - Kubernetes 클러스터 만들기'
    module: '모듈 2 - 플랫폼 보호 구현'
---

# 모듈 2: 랩 3 - Kubernetes 클러스터 만들기


AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리하는 데 사용할 수 있는 관리 Kubernetes 서비스입니다. 이 빠른 시작에서는 Azure CLI를 사용하여 AKS 클러스터를 배포하고, 웹 프런트 엔드 및 Redis Cache 인스턴스를 포함하는 다중 컨테이너 애플리케이션을 클러스터에서 실행합니다. 그런 다음 애플리케이션을 실행하는 클러스터 및 Pod의 상태를 모니터링하는 방법을 알아봅니다.

## 연습 1: AKS 환경 만들기

### 태스크 1: 환경 준비 및 리소스 그룹 만들기

1.  브라우저를 열고 **Azure Portal**(**`https://portal.azure.com`**)로 이동합니다.

1.  **Cloudshell** 아이콘을 클릭합니다.

     ![스크린샷](../Media/Module-2/4efbdec1-f1c9-4c37-8ca5-193f245a274d.png)

1.  필요한 경우 **Azure CLI BASH** 를 선택하고 스토리지 계정을 만듭니다.

1.  **Cloud Shell** 에서 다음 명령을 실행하여 새 **리소스 그룹** 을 만듭니다.

     ```cli
    az group create --name myResourceGroup --location eastus
     ```

### 태스크 2: CLI에서 AKS 클러스터 만들기

1.  **Cloud Shell** 에서 다음 명령을 실행합니다.

     ```cli
    az aks create  --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
     ```
 
1.  몇 분 후 명령이 완료되고 클러스터 관련 **JSON 형식** 정보가 반환됩니다.

### 태스크 3: 클러스터에 연결


Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 kubectl을 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다.


1.  **Azure Cloud Shell** 에서 다음 명령을 입력합니다.

     ```azurecli
    az aks install-cli
     ```


1.  **Kubernetes 클러스터** 에 연결하도록 `kubectl`을 구성하려면 az-aks-get-credentials 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 이 자격 증명을 사용하도록 **Kubernetes CLI** 를 구성합니다.


     ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
     ```

1.  클러스터에 대한 연결을 확인하려면 kubectl-get 명령을 사용하여 클러스터 노드 목록을 반환합니다.


    ```azurecli-interactive
    kubectl get nodes
    ```

1.  다음 예제 출력은 이전 단계에서 생성된 단일 노드를 보여 주며, 이 출력은 다음과 같은 것입니다. 노드의 상태가 *Ready* 인지 확인합니다.

    ```json
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.9.11
    ```

### 태스크 4: 애플리케이션 실행


Kubernetes 매니페스트 파일은 실행할 컨테이너 이미지 등 클러스터의 필요한 상태를 정의합니다. 이 랩에서는 매니페스트를 사용하여 Azure Vote 애플리케이션을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 Kubernetes 배포 두 개가 포함됩니다. 그 중 하나는 샘플 Azure Vote Python 애플리케이션용이고 다른 하나는 Redis 인스턴스용입니다. Kubernetes 서비스도 두 개 만듭니다. 그 중 하나는 Redis 인스턴스용 서비스이고, 다른 하나는 인터넷에서 Azure Vote 애플리케이션에 액세스하는 데 사용되는 외부 서비스입니다. 매니페스트 파일은 생성되어 이 랩용 godeploy GitHub 페이지에 저장된 상태입니다. 이 파일(azure-vote.yaml)은 **`https://raw.githubusercontent.com/MicrosoftLearning/AZ-500-Azure-Security/master/Allfiles/Labs/Mod2_Lab03/azure-vote.yaml`** 에서 제공됩니다.



1.  Cloud Shell에서 다음 명령을 실행하면 AKS 애플리케이션을 배포하는 데 필요한 yaml 파일을 godeploy GitHub에서 직접 끌어옵니다.

     ```cli
    kubectl apply -f https://raw.githubusercontent.com/MicrosoftLearning/AZ-500-Azure-Security/master/Allfiles/Labs/Mod2_Lab03/azure-vote.yaml
     ```

2.  다음 예제 출력에는 **배포 및 서비스** 가 정상적으로 작성되었다는 내용이 표시됩니다.

     ```json
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
     ```

**참고**: 애플리케이션이 실행되면 Kubernetes 서비스가 애플리케이션 프런트 엔드를 인터넷에 표시합니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.


### 태스크 5: 애플리케이션 테스트


애플리케이션이 실행되면 Kubernetes 서비스가 애플리케이션 프런트 엔드를 인터넷에 표시합니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.


1.  진행 상황을 모니터링하려면 `--watch` 인수를 포함하여 kubectl get 명령을 사용합니다.

     ```azurecli-interactive
    kubectl get service azure-vote-front --watch
     ```

1.  처음에는 *azure-vote-front* 서비스용 *EXTERNAL-IP* 가 *pending* 으로 표시됩니다.

     ```
    NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)         AGE
    azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP    6s
     ```


1.  *EXTERNAL-IP* 주소가 *pending* 에서 실제 공용 IP 주소로 변경되면 `Ctrl+C`를 눌러 `kubectl` 감시 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

     ```
    azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/    TCP   2m
     ```

2.  Azure Vote 앱의 실제 작동 방식을 확인하려면 이전 명령의 결과에 나와 있는 것처럼 웹 브라우저에서 서비스의 외부 IP 주소를 엽니다.

     ![스크린샷](../Media/Module-2/88d51dc5-a992-436f-a65e-83a766c142a9.png)


### 태스크 6: 상태 및 로그 모니터링


AKS 클러스터를 만들 때 클러스터 노드와 Pod에 대한 상태 메트릭을 캡처하는 컨테이너용 Azure Monitor가 사용되도록 설정되었습니다. 이러한 상태 메트릭은 Azure Portal에서 사용할 수 있습니다.


Azure Vote pod의 현재 상태, 가동 시간 및 리소스 사용량을 보려면 다음 단계를 완료하십시오:

1.  웹 브라우저에서 Azure Portal을 엽니다.

1.  *myResourceGroup* 과 같은 리소스 그룹을 선택한 다음 *myAKSCluster* 와 같은 AKS 클러스터를 선택합니다.
1.  왼쪽의 **모니터링** 아래에서 **Insights** 를 선택합니다.
1.  위쪽의 **+필터 추가** 를 선택합니다.
1.  *네임스페이스* 를 속성으로 선택한 다음 *\<kube-system을 제외한 모든 항목\>* 을 선택합니다.
1.  **컨테이너** 를 선택하여 표시합니다.

    *azure-vote-back* 및 *azure-vote-front* 컨테이너가 표시됩니다.


1.  'azure-vote-front' pod에 대한 로그를 보려면 컨테이너 목록의 오른쪽에 있는 **컨테이너 로그 보기** 링크를 선택합니다. 이러한 로그에는 컨테이너의 *stdout* 및 *stderr* 스트림이 포함됩니다.


### 태스크 7: 클러스터 삭제


클러스터가 더 이상 필요하지 않으면 **`az group delete`** 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.


1.  클러스터를 삭제하려면 다음 명령을 실행합니다.

     ```cli
    az group delete --name myResourceGroup --yes --no-wait
     ```


**결과**: 이 랩이 완료되었습니다.
