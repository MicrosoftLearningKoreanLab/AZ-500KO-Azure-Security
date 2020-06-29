---
lab:
    title: 'LAB 04_가상 네트워크 만들기'
    module: '모듈 02 - 플랫폼 보호'
---

# 랩: 가상 네트워크 만들기

이 모듈에서는 Azure 가상 네트워크를 소개합니다. 가상 네트워크란 무엇이며 어떻게 구성 되는지 템플릿, PowerShell, CLI 또는 Azure Portal을 사용하여 가상 네트워크를 어떻게 만들고 구성하는지 퍼블릭, 프라이빗, 정적 및 동적 IP 주소 지정의 차이점은 무엇인지 시스템 경로, 라우팅 테이블 및 라우팅 알고리즘은 어떻게 사용되는지 알아봅니다. 이번 랩에서 알아볼 내용은 다음과 같습니다.

- 가상 네트워크 소개
- 가상 네트워크 만들기
- IP 주소 리뷰

### 연습 1: Azure Portal을 사용하여 가상 네트워크 만들기

가상 네트워크는 Azure의 개인 네트워크를 위한 기본 구성입니다. VM(가상 컴퓨터)과 같은 Azure 리소스가 서로 안전하게 통신 할 수 있습니다. 이 연습에서는 Azure Portal을 사용하여 가상 네트워크를 만드는 방법을 배웁니다. 그런 다음 두 개의 VM을 가상 네트워크에 배포하고 두 VM간에 안전하게 통신하고 인터넷에서 VM에 연결할 수 있습니다.

#### 작업 1: 가상 네트워크 만들기

1. <a href="https://portal.azure.com" target="_blank"><span style="color: #0066cc;" color="#0066cc">Azure Portal</span></a>에 로그인 합니다.

1. **+ 리소스 만들기**를 클릭한 다음 **virtual network**를 입력하고 **virtual network**를 클릭한다.

1. **가상 네트워크** 블레이드가 뜨면 **만들기** 버튼을 클릭한다.

1. **가상 네트워크 만들기** 블레이드가 뜨면 다음을 참고하여 정보를 입력한다.

    | 설정 | 값 |
    | --- | --- |
    | 이름 | **az5000204vnet** |
    | 주소 공간 | **10.1.0.0/16** |
    | 구독 | 이 랩에서 사용할 구독 |
    | 리소스 그룹 | **az5000204**로 새로 만들기 |
    | 위치 | **(아시아 태평양)한국 중부** |
    | 서브넷 - 이름 | **Subnet** |
    | 서브넷 - 주소 범위 | **10.1.0.0/24** |

1. 나머진 기본값으로 두고 **만들기** 버튼을 클릭한다.

#### 작업 2: 가상 머신 만들기

생성한 가상 네트워크에 두 개의 가상 머신을 배포합니다.

1. **+ 리소스 만들기**를 클릭한 다음 **windows server**를 입력하고 **Windows Server**를 클릭한다.

1. **Windows Server** 블레이드가 뜨면 소프트웨어 플랜 선택 드롭다운 메뉴를 클릭하여 **Windows Server 2019 Datacenter**를 선택한 후 **만들기** 버튼을 클릭한다.

1. **가상 머신 만들기** 블레이드가 뜨면 다음을 참고하여 정보를 입력한다.

    | 설정 | 값 |
    | --- | --- |
    | 구독 | 이 랩에서 사용할 구독 |
    | 리소스 그룹 | **az5000204** |
    | 가상 머신 이름 | **az5000204vm01** |
    | 지역 | **(아시아 태평양)한국 중부** |
    | 이미지 | **Windows Server 2019 Datacenter** |
    | 크기 | **Standard DS2 v2** |
    | 사용자 이름 | **student** |
    | 암호 | **Pa55w.rd1234** |
    | 공용 인바운드포트 | **선택한 포트 허용** |
    | 인바운드 포트 선택 | **HTTP**와 **RDP** |

1. **다음: 디스크** 버튼을 클릭한다.

1. **다음: 네트워킹** 버튼을 클릭한다.

1. 네트워킹 탭에서 다음을 참고하여 정보를 입력한다.

    | 설정 | 값 |
    | --- | --- |
    | 가상 네트워크 | **az5000204vnet** |
    | 서브넷 | **Subnet(10.1.0.0/24)** |
    | 공용 IP | **(새로 만드는 중) az5000204vm01-ip**. |
    | 공용 인바운드 포트 | **선택한 포트 허용** |
    | 인바운드 포트 선택 | **HTTP**와 **RDP** |

1. **다음: 관리** 버튼을 클릭한다.

1. 관리 탭에서 **진단 스토리지 계정**에 있는 **새로 만들기**를 클릭한다.

1. 오른쪽에 **스토리지 계정 만들기** 컨텍스트 창이 뜨면 다음을 참고하여 입력하고 **확인** 버튼을 클릭한다.

    | 설정 | 값 |
    | --- | --- |
    | 이름 | **az5000204diagxxx** (xxx는 유니크 해야 함) |
    | 계정 종류 | **Storage (범용 v1)**. |
    | 성능 | **표준**. |
    | 복제 | **LRS(로컬 중복 스토리지)** |

1. **검토 + 만들기** 버튼을 클릭한 후 유효성 검사가 완료되면 **만들기** 버튼을 클릭하여 가상 머신을 배포한다.

> **참고**: 배포가 완료되길 기다리지 않고 다음을 진행해도 됩니다.

#### 작업 3: 두번째 VM 배포

1. 다음 정보를 참고하여 배포된 VM과 동일한 설정으로 두번째 VM을 배포한다.

    - **기본 사항**

    | 설정 | 값 |
    | --- | --- |
    | 구독 | 이 랩에서 사용할 구독 |
    | 리소스 그룹 | **az5000204** |
    | 가상 머신 이름 | **az5000204vm02** |
    | 지역 | **(아시아 태평양)한국 중부** |
    | 이미지 | **Windows Server 2019 Datacenter** |
    | 크기 | **Standard DS2 v2** |
    | 사용자 이름 | **student** |
    | 암호 | **Pa55w.rd1234** |
    | 공용 인바운드포트 | **없음** |

    - **네트워킹**

    | 설정 | 값 |
    | --- | --- |
    | 가상 네트워크 | **az5000204vnet** |
    | 서브넷 | **Subnet(10.1.0.0/24)** |
    | 공용 IP | **(새로 만드는 중) az5000204vm02-ip**. |
    | 공용 인바운드 포트 | **선택한 포트 허용** |
    | 인바운드 포트 선택 | **HTTP**와 **RDP** |

    - **관리**

    | 설정 | 값 |
    | --- | --- |
    | 진단 스토리지 계정 | **az5000204diagxxx** (az5000204vm01에서 생성한 스토리지 계정) |

1. **검토 + 만들기** 버튼을 클릭한 후 유효성 검사가 완료되면 **만들기** 버튼을 클릭하여 가상 머신을 배포한다.

#### 작업 4: 가상 머신에 연결

1. Azure Portal에서 생성한 **az5000204vm01**을 탐색한다.

1. **연결**을 클릭한다.

1. 오른쪽에 **가상 머신에 연결** 컨텍스트 창이 뜨면 **RDP 파일 다운로드** 버튼을 클릭하여 *.rdp* 파일을 다운로드 한다.

    > **참고**: Mac OS의 경우 App Store에서 Microsoft에서 배포한 Remote Desktop App을 설치한 후 접속합니다.

1. 다운로드 받은 *.rdp* 파일을 실행한다.

1. 앞서 입력한 사용자 이름과 암호를 이용하여 Windows Server 2019 Datacenter에 로그인 한다.

#### 작업 5: VM간 통신 확인

1. 접속한 **az5000204vm01**에서 PowerShell(또는 Command Line)을 실행한다.

1. 다음 명령어를 이용하여 **az5000204vm02**과 통신이 되는지 확인한다.

    ```PowerShell
    ping az5000204vm02
    ```

    ```Execute
    Pinging az5000204vm02.iui5rgnfs22ublc3xy34urwtwa.syx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

1. `ping` 명령어가 실패하는 것을 확인할 수 있다. 이는 Windows Server의 운영체제 방화벽에서 기본적으로 ICMP 패킷을 차단하기 때문이다. **az5000204vm01**에 접속한 방법을 사용하여 **az5000204vm02**에 접속한다.

1. **az5000204vm02**에 접속하여 PowerShell을 실행하고 다음 명령어를 입력하여 운영체제 방화벽에서 ICMP 프로토콜을 허용한다.

    ```powershell
    New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
    ```

1. **az5000204vm01**에 다시 연결한다.

1. **az5000204vm01**에서 PowerShell을 실행하여 다음 명령어를 이용하여 **az5000204vm02**과 통신이 되는지 확인한다.

    ```PowerShell
    ping az5000204vm02
    ```

    ```Execute
    Pinging az5000204vm02.iui5rgnfs22ublc3xy34urwtwa.syx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

1. 두 가상 머신간 통신이 정상적임을 확인한다.

### 연습 2: 랩 리소스 삭제

#### 작업 1: Cloud Shell 열기

1. Azure 포털 상단에서 **Cloud Shell** 아이콘을 클릭하여 Cloud Shell 창을 시작한다.

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
