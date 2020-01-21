# AZ-500 애플리케이션 보안

- **MCT이신가요?** - Microsoft의 [MCT용 GitHub 사용자 가이드](https://microsoftlearning.github.io/MCT-User-Guide/)를 살펴보세요.
- **랩 지침을 수동으로 작성해야 하나요?** - [MicrosoftLearning/Docker-Build](https://github.com/MicrosoftLearning/Docker-Build) 리포지토리에서 지침이 제공됩니다.

> [MCT 교육 과정 자료 포럼](https://www.microsoft.com/ko-kr/learning/mct-central.aspx)에서 과정 콘텐츠 관련 제안 사항이나 일반 설명을 확인하세요. [교육 과정 자료 지원 포럼](https://trainingsupport.microsoft.com/ko-kr)에서 버그 및 과정 오류를 보고할 수도 있습니다.
 
신규 변경 내용을 지원하기 위해 2019년 11월 1일부터 신규 AZ-500 GitHub 리포지토리가 도입되었습니다. 이 시점부터 모든 AZ-500 랩 대신 이 리포지토리가 제공됩니다.

**GitHub 소개**

*	코스 작성자와 MCT가 상호 작용을 할 수 있도록 GitHub에 랩 지침과 랩 파일을 게시하고 있습니다. 이를 통해 Azure 플랫폼이 변경되어도 콘텐츠를 최신 상태로 유지하기 위해 노력하고 있습니다.

*	이 리포지토리는 AZ-500 Microsoft Azure 보안 과정용 GitHub 리포지토리입니다. 

*	각 리포지토리 내의 Instructions 폴더에는 Markdown 형식의 랩 가이드가 있습니다. 해당하는 경우 Allfiles\Labfiles 내에 랩을 완료하는 데 필요한 추가 파일도 포함되어 있습니다. 모든 과정에 해당하는 랩 파일이 있는 것은 아닙니다. 

*	교육 담당자는 제공되는 각 콘텐츠와 관련하여 GitHub에서 최신 파일을 다운로드해야 합니다. 또한 문제 탭에서 다른 MCT가 오류를 보고했는지도 확인해야 합니다.  

*	예상 랩 소요 시간이 제공되기는 하지만, 교육 담당자는 대상 그룹을 기준으로 해당 시간이 정확한지를 확인해야 합니다.

*	랩을 진행하려면 인터넷에 연결할 수 있어야 하며 Azure 구독을 보유하고 있어야 합니다. Cloud Shell 사용법과 관련된 자세한 내용은 강사 준비 가이드를 참조하세요. 

**GitHub 운영 방식**

*	이러한 과정의 교육을 진행하면서 개선이 필요한 부분이 확인되면 문제 탭을 통해 피드백을 제공해 주시기 바랍니다. Microsoft에서는 정기적으로 새 파일을 만들어 변경 내용을 통합합니다. 


* Azure Cloud Shell을 처음 시작하면 Cloud Shell 파일을 저장할 Azure 파일 공유를 만들라는 메시지가 표시될 수 있습니다. 이 경우에는 대개 기본값을 적용하면 됩니다. 그러면 자동으로 생성된 리소스 그룹에 스토리지 계정이 만들어집니다. 해당 스토리지 계정을 삭제하면 이 메시지가 다시 표시될 수 있습니다.

* 템플릿 기반 배포를 수행하기 전에는 템플릿에서 참조하는 리소스 종류의 프로비전을 처리하는 공급자를 등록해야 할 수 있습니다. 이러한 리소스 공급자를 아직 등록하지 않은 경우 Azure Resource Manager 템플릿을 사용하여 해당 공급자가 관리하는 리소스를 배포할 때 등록 작업을 구독당 한 번만 수행하면 됩니다. Azure Portal의 구독 리소스 공급자 블레이드에서 등록을 수행할 수도 있고, Cloud Shell을 사용해 Register-AzResourceProvider PowerShell cmdlet 또는 az provider Azure CLI 명령을 사용할 수도 있습니다.

이 GitHub 리포지토리를 활용하여 랩에서 공동 작업을 원활하게 진행하고 전반적인 랩 환경 품질을 개선하시기 바랍니다. 

감사합니다.
*Azure 보안 교육 과정 자료 팀*