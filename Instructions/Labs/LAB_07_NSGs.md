---
lab:
    title: '07 - 네트워크 보안 그룹 및 애플리케이션 보안 그룹'
    module: '모듈 02 - 플랫폼 보호 구현'
---

# 랩 07: 네트워크 보안 그룹 및 애플리케이션 보안 그룹
# 학생 랩 매뉴얼

## 랩 시나리오

조직의 가상 네트워킹 인프라를 구현하고 올바르게 작동하는지 테스트하라는 요청이 있습니다. 특히,

- 조직에는 두 개의 서버 그룹이 있습니다. 웹 서버 및 관리 서버.
- 각 서버 그룹은 자체 애플리케이션 보안 그룹에 있어야 합니다. 
- 웹 서버가 아닌 관리 서버로 RDP를 할 수 있어야 합니다.
- 웹 서버는 인터넷에서 액세스할 때 IIS 웹 페이지를 표시해야 합니다. 
- 네트워크 보안 그룹 규칙을 사용하여 네트워크 액세스를 제어해야 합니다. 

> 이 랩에 있는 모든 리소스의 경우 **미국 동부** 지역을 사용합니다. 강사에게 이 지역이 수업에 사용할 지역인지 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다.

- 연습 1: 가상 네트워킹 인프라 만들기
- 연습 2: 가상 머신 배포 및 네트워크 필터 테스트

### 연습 1: 가상 네트워킹 인프라 만들기

### 예상 시간: 20분

> 이 랩에 있는 모든 리소스의 경우 **미국 동부** 지역을 사용합니다. 강사에게 이 지역을 수업에서 사용하는지 확인합니다. 

이 연습에서는 다음 작업을 수행합니다.

- 작업 1: 하나의 서브넷이 있는 가상 네트워크를 만듭니다.
- 작업 2: 두 개의 애플리케이션 보안 그룹을 만듭니다.
- 작업 3: 네트워크 보안 그룹을 만들고 가상 네트워크 서브넷과 연결합니다.
- 작업 4: 웹 서버의 모든 트래픽 및 관리 서버의 RDP에 인바운드 NSG 보안 규칙을 만듭니다.

#### 작업 1:  가상 네트워크 만들기

이 작업에서는 네트워크 및 애플리케이션 보안 그룹과 함께 사용할 가상 네트워크를 만듭니다. 

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

1. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 네트워크**를 입력하고 **Enter** 키를 누릅니다.

1. **가상 네트워크** 블레이드에서 **+ 만들기**를 클릭합니다.

1. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고(다른 설정은 기본값 그대로 둡니다) **다음: IP 주소**:

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**새로 만들기**를 선택하고 이름 **AZ500LAB07**를 입력합니다.|
    |이름|**myVirtualNetwork**|
    |지역|**미국 동부**|

1. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **IPv4 주소 공간**을 **10.0.0.0/16**으로 설정하고 필요한 경우 **서브넷 이름** 열에서 **기본값**을 클릭하고 **서브넷 편집** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

    |설정|값|
    |---|---|
    |서브넷 이름|**기본값**|
    |서브넷 주소 범위|**10.0.0.0/24**|

1. 다시 **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **검토 + 만들기**를 클릭합니다.

1. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.

#### 작업 2:  애플리케이션 보안 그룹 만들기

이 작업에서는 애플리케이션 보안 그룹을 만듭니다.

1. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **애플리케이션 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

1. **애플리케이션 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

1. **애플리케이션 보안 그룹 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다: 

    |설정|값|
    |---|---|
    |리소스 그룹|**AZ500LAB07**|
    |이름|**myAsgWebServers**|
    |지역|**미국 동부**|

    >**참고**: 이 그룹은 웹 서버용입니다.

1. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

1. **애플리케이션 보안 그룹** 블레이드로 다시 이동하여 **+ 만들기**를 클릭합니다.

1. **애플리케이션 보안 그룹 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다: 

    |설정|값|
    |---|---|
    |리소스 그룹|**AZ500LAB07**|
    |이름|**myAsgMgmtServers**|
    |지역|**미국 동부**|

    >**참고**: 이 그룹은 관리 서버용입니다.

1. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

#### 작업 3:  네트워크 보안 그룹을 만들고 NSG를 서브넷에 연결합니다.

이 작업에서는 네트워크 보안 그룹을 만듭니다. 

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **네트워크 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

1. **네트워크 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

1. **네트워크 보안 그룹 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |리소스 그룹|**AZ500LAB07**|
    |이름|**myNsg**|
    |지역|**미국 동부**|

1. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

1. Azure Portal에서 **네트워크 보안 그룹** 블레이드로 돌아가 **myNsg** 항목을 클릭합니다.

1. **myNsg** 블레이드의 **설정** 섹션에서 **서브넷**을 클릭한 다음 **+ 연결**을 클릭합니다. 

1. **서브넷 연결** 블레이드에서 다음 설정을 지정하고 **확인**을 클릭합니다.

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**기본값**|

#### 작업 4: 웹 서버의 모든 트래픽 및 관리 서버의 RDP에 인바운드 NSG 보안 규칙을 만듭니다. 

1. **myNsg** 블레이드의 **설정** 섹션에서 **인바운드 보안 규칙**을 클릭합니다.

1. 기본 인바운드 보안 규칙을 검토한 다음 **+ 추가**를 클릭합니다.

1. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정하여 TCP 포트 80 및 443을 **myAsgWebServers** 애플리케이션 보안 그룹에 허용합니다(다른 모든 값은 기본값으로 남겨 둡니다). 

    |설정|값|
    |---|---|
    |대상|드롭다운 목록에서 **애플리케이션 보안 그룹**을 선택한 다음 **myAsgWebServers**를 클릭합니다|
    |대상 포트 범위|**80,443**|
    |프로토콜|**TCP**|
    |우선 순위|**100**|                                                    
    |이름|**Allow-Web-All**|

1. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

1. **설정** 섹션의 **myNsg** 블레이드에서 **인바운드 보안 규칙**을 클릭한 다음 **+ 추가**를 클릭합니다.

1. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정하여 RDP 포트(TCP 3389)를 **myAsgMgmtServers** 애플리케이션 보안 그룹에 허용합니다(다른 모든 값은 기본값으로 남겨 둡니다). 

    |설정|값|
    |---|---|
    |대상|드롭다운 목록에서 **애플리케이션 보안 그룹**을 선택한 다음 **myAsgMgmtServers**를 클릭|
    |대상 포트 범위|**3389**|
    |프로토콜|**TCP**|
    |우선 순위|**110**|                                                    
    |이름|**Allow-RDP-All**|

1. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

> 결과: 가상 네트워크, 인바운드 보안 규칙을 가진 네트워크 보안 그리고 두개의 애플리케이션 보안 그룹을 배포하였습니다. 

### 연습 2: 가상 머신 배포 및 네트워크 필터 테스트

### 예상 시간: 25분

이 연습에서는 다음 작업을 수행합니다.

- 작업 1: 웹 서버로 사용할 가상 머신 만들기
- 작업 2: 관리 서버로 사용할 가상 머신 만들기 
- 작업 3: 각 가상 머신 네트워크 인터페이스를 애플리케이션 보안 그룹에 연결
- 작업 4: 네트워크 트래픽 필터링 테스트

#### 작업 1: 웹 서버로 사용할 가상 머신 만들기

이 작업에서는 웹 서버로 사용할 가상 머신을 만듭니다.

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

1. **가상 머신** 블레이드에서 **+ 만들기**를 클릭하고 드롭다운 목록에서 **+ 가상 머신**을 클릭합니다.

1. **가상 머신 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |리소스 그룹|**AZ500LAB07**|
   |가상 머신 이름|**myVmWeb**|
   |지역|**(미국)미국 동부**|
   |이미지|**Windows Server 2019 Datacenter - Gen2**|
   |크기|**Standard D2s v3**|
   |사용자 이름|**Student**|
   |암호|**Pa55w.rd1234**|
   |암호 확인|**Pa55w.rd1234**|
   |공용 인바운드 포트|**None**|
   |기존 Windows 서버 라이선스를 사용하시겠습니까? |**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

1. **다음: 디스크 >** 를 클릭하고 **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형** 을 **표준 HDD** 로 설정하고 **다음:** 을 클릭합니다.**네트워킹 >** 을 차례로 클릭합니다.

1. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 이전에 만든 네트워크인 **myVirtualNetwork**를 선택합니다.

1. **NIC 네트워크 보안 그룹** 아래에서 **없음**을 선택합니다.

1. **다음: 관리 >** 를 클릭하고 **가상 머신 만들기** 블레이드의 **관리** 탭에서 다음 설정을 확인합니다.

   |설정|값|
   |---|---|
   |부팅 진단|**관리 스토리지 계정 사용(권장)**|

1. **검토 + 만들기** 블레이드에서 **검토 + 만들기**를 클릭하고 유효성 검사가 성공하였는지 확인한 다음 **만들기**를 클릭합니다.

#### 작업 2: 관리 서버로 사용할 가상 머신 만들기 

이 작업에서는 관리 서버로 사용할 가상 머신을 만듭니다.

1. Azure Portal의 **가상 머신** 블레이드로 다시 이동하여 **+ 만들기**를 클릭하고 드롭다운 목록에서 **+ 가상 머신**을 클릭합니다.

1. **가상 머신 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |리소스 그룹|**AZ500LAB07**|
   |가상 머신 이름|**myVMMgmt**|
   |지역|(미국)미국 동부|
   |이미지|**Windows Server 2019 Datacenter Gen 2**|
   |크기|**Standard D2s v3**|
   |사용자 이름|**Student**|
   |암호|**Pa55w.rd1234**|
   |공용 인바운드 포트|**None**|
   |이미 Windows Server 라이선스가 있으신가요?|**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

1. **다음: 디스크 >** 를 클릭하고 **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형** 을 **표준 HDD** 로 설정하고 **다음:** 을 클릭합니다.**네트워킹 >** 을 차례로 클릭합니다.

1. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 이전에 만든 네트워크인 **myVirtualNetwork**를 선택합니다.

1. **NIC 네트워크 보안 그룹** 아래에서 **없음**을 선택합니다.

1. **다음: 관리 >** 를 클릭하고 **가상 머신 만들기** 블레이드의 **관리** 탭에서 다음 설정을 지정합니다.

   |설정|값|
   |---|---|
   |부팅 진단|**관리 스토리지 계정 사용(권장)**|

1. **검토 + 만들기** 블레이드에서 **검토 + 만들기**를 클릭하고 유효성 검사가 성공하였는지 확인한 다음 **만들기**를 클릭합니다.

    >**참고**: 가상 머신이 모두 프로비저닝될 때까지 기다린 후에 계속 진행합니다. 

#### 작업 3: 각 가상 머신 네트워크 인터페이스를 애플리케이션 보안 그룹에 연결

이 작업에서는 각 가상 머신 네트워크 인터페이스를 해당 애플리케이션 보안 그룹과 연결합니다. myVMWeb 가상 머신 인터페이스는 myAsgWebServers ASG에 연결됩니다. myVMMgmt 가상 머신 인터페이스는 myAsgMgmtServers ASG에 연결됩니다. 

1. Azure Potal에서 **가상 머신** 블레이드로 돌아가서 두 가상 머신 모두 **실행** 상태에 나열되었는지 확인합니다.

1. 가상 머신 목록에서 **myVMWeb** 항목을 클릭합니다.

1. **myVMWeb** 블레이드의 **설정** 섹션에서 **네트워킹**을 클릭한 다음 **myVMWeb \| 네트워킹** 블레이드에서 **애플리케이션 보안 그룹** 탭을 클릭합니다.

1. **애플리케이션 보안 그룹 구성**을 클릭하고 **애플리케이션 보안 그룹** 드롭다운 목록에서 **myAsgWebServer**를 선택한 다음 **저장**을 클릭합니다.

1. **가상 머신** 블레이드로 돌아가서 가상 머신 목록에서 **myVMMgmt** 항목을 클릭합니다.

1. **myVMMgmt** 블레이드의 **설정** 섹션에서 **네트워킹**을 클릭한 다음 **myVMMgmt \| 네트워킹** 블레이드에서 **애플리케이션 보안 그룹** 탭을 클릭합니다.

1. **애플리케이션 보안 그룹 구성**을 클릭하고 **애플리케이션 보안 그룹** 드롭다운 목록에서 **myAsgMgmtServers**를 선택한 다음 **저장**을 클릭합니다.

#### 작업 4: 네트워크 트래픽 필터링 테스트

이 작업에서는 네트워크 트래픽 필터를 테스트합니다. myVMMgmnt 가상 머신에 RDP를 할 수 있어야 합니다. 인터넷에서 myVMWeb 가상 머신으로 연결하고 기본 IIS 웹 페이지를 볼 수 있어야 합니다.  

1. **myVMMgmt** 가상 머신 블레이드로 돌아갑니다.

1. **myVMMgmt** 블레이드에서 **연결**을 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

1. **RDP 파일 다운로드**를 클릭하고 원격 데스크톱을 통해 **myVMMgmt** Azure VM에 연결하는 데 사용합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

   |설정|값|
   |---|---|
   |사용자 이름|**Student**|
   |암호|**Pa55w.rd1234**|

    >**참고**: 원격 데스크톱 연결이 성공했는지 확인합니다. 이 시점에서 원격 데스크톱을 통해 myVMMgmt에 연결할 수 있다는 것을 확인하였습니다.

1. Azure Portal에서 **myVMWeb** 가상 머신 블레이드로 이동합니다.

1. **myVMWeb** 블레이드의 **운영** 섹션에서 **명령 실행**을 클릭한 다음 **RunPowerShellScript**를 클릭합니다.

1. **명령 스크립트 실행** 창에서 다음을 실행하여 **myVmWeb**에 웹 서버 역활을 설치합니다.

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**참고**: 설치가 완료될 때까지 기다립니다. 이 과정은 몇 분 정도 걸릴 수 있습니다. 이 시점에서 HTTP/HTTPS를 통해 myVMWeb에 액세스할 수 있는지 확인할 수 있습니다.

1. Azure Portal에서 **myVMWeb** 블레이드로 이동합니다.

1. **myVMWeb** 블레이드에서 myVmWeb Azure VM의 **공용 IP 주소**를 식별합니다.

1. 다른 브라우저 탭을 열고 이전 단계에서 식별한 IP 주소로 이동합니다.

    >**참고**: 포트 80은 **myAsgWebServers** 애플리케이션 보안 그룹의 설정에 따라 인터넷에서 인바운드가 허용되므로 기본 IIS 시작 페이지가 브라우저 페이지에 표시되어야 합니다. myVMWeb Azure VM의 네트워크 인터페이스는 해당 애플리케이션 보안 그룹과 연결됩니다. 

> 결과: NSG 및 ASG 구성이 작동하고 트래픽이 올바르게 관리되고 있는지 확인했습니다. 

**리소스 정리**

> 더 이상 사용하지 않는 새로 만든 Azure 리소스를 제거해야 합니다. 사용하지 않는 리소스를 제거하면 예기치 않은 비용이 발생하지 않습니다. 

1. Azure Portal 오른쪽 상단에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell** 및 **스토리지 만들기**를 선택합니다.

1. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

1. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

1.  **Cloud Shell** 창을 닫습니다. 