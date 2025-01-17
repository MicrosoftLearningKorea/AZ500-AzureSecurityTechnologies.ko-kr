---
lab:
  title: 07 - 네트워크 보안 그룹 및 애플리케이션 보안 그룹
  module: Module 02 - Implement Platform Protection
---

# 랩 07: 네트워크 보안 그룹 및 애플리케이션 보안 그룹
# 학생용 랩 매뉴얼

## 랩 시나리오

조직의 가상 네트워킹 인프라를 구현하고 올바르게 작동하는지 테스트하라는 요청이 있습니다. 특히 다음 사항에 주의하십시오.

- 조직에는 두 개의 서버 그룹이 있습니다. 웹 서버 및 관리 서버.
- 각 서버 그룹은 자체 애플리케이션 보안 그룹에 있어야 합니다. 
- 웹 서버가 아닌 관리 서버로 RDP를 할 수 있어야 합니다.
- 웹 서버는 인터넷에서 액세스할 때 IIS 웹 페이지를 표시해야 합니다. 
- 네트워크 보안 그룹 규칙을 사용하여 네트워크 액세스를 제어해야 합니다. 

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 이 지역을 수업에 사용할 것인지 강사에게 확인합니다. 

## 랩 목표

이 랩에서는 다음과 같은 연습을 완료합니다:

- 연습 1: 가상 네트워킹 인프라 만들기
- 연습 2: 가상 머신 배포 및 네트워크 필터 테스트

## 네트워크 및 애플리케이션 보안 그룹 다이어그램

![이미지](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## Instructions

### 연습 1: 가상 네트워킹 인프라 만들기

### 예상 소요 시간: 20분

> 이 랩의 모든 리소스에 대해 **미국 동부** 지역을 사용하고 있습니다. 강사에게 이 지역을 수업에서 사용하는지 확인합니다. 

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 하나의 서브넷이 있는 가상 네트워크를 만듭니다.
- 작업 2: 두 개의 애플리케이션 보안 그룹을 만듭니다.
- 작업 3: 네트워크 보안 그룹을 만들고 가상 네트워크 서브넷과 연결합니다.
- 작업 4: 웹 서버의 모든 트래픽 및 관리 서버의 RDP에 인바운드 NSG 보안 규칙을 만듭니다.

#### 작업 1:  가상 네트워크 만들기

이 작업에서는 네트워크 및 애플리케이션 보안 그룹과 함께 사용할 가상 네트워크를 만듭니다. 

1. Azure Portal **`https://portal.azure.com/`** 에 로그인합니다.

    >**참고**: 이 랩에 사용 중인 Azure 구독에 Owner 또는 Contributor 역할이 있는 계정을 사용하여 Azure Portal에 로그인합니다.

2. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 네트워크**를 입력하고 **Enter** 키를 누릅니다.

3. **가상 네트워크** 블레이드에서 **+ 만들기**를 클릭합니다.

4. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고(다른 설정은 기본값으로 둡니다) **다음: IP 주소**를 클릭합니다.

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**새로 만들기**를 클릭하고 **AZ500LAB07**을 이름으로 입력합니다.|
    |이름|**myVirtualNetwork**|
    |지역|**미국 동부**|

5. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **IPv4 주소 공간**을 **10.0.0.0/16**으로 설정하고 필요한 경우 **서브넷 이름** 열에서 **기본값**을 클릭하고 **서브넷 편집** 블레이드에서 다음 설정을 지정하고 **저장**을 클릭합니다.

    |설정|값|
    |---|---|
    |서브넷 이름|**default**|
    |서브넷 주소 범위|**10.0.0.0/24**|

6. 다시 **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **검토 + 만들기**를 클릭합니다.

7. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.

#### 작업 2:  애플리케이션 보안 그룹 만들기

이 작업에서는 애플리케이션 보안 그룹을 만듭니다.

1. Azure Portal 내 Azure Portal 페이지 상단의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **애플리케이션 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

2. **애플리케이션 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

3. **애플리케이션 보안 그룹 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다: 

    |설정|값|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |이름|**myAsgWebServers**|
    |지역|**미국 동부**|

    >**참고**: 이 그룹은 웹 서버용입니다.

4. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

5. **애플리케이션 보안 그룹** 블레이드로 다시 이동하여 **+ 만들기**를 클릭합니다.

6. **애플리케이션 보안 그룹 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다: 

    |설정|값|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |이름|**myAsgMgmtServers**|
    |지역|**미국 동부**|

    >**참고**: 이 그룹은 관리 서버용입니다.

7. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

#### 작업 3:  네트워크 보안 그룹을 만들고 NSG를 서브넷에 연결합니다.

이 작업에서는 네트워크 보안 그룹을 만듭니다. 

1. Azure Portal에서 Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **네트워크 보안 그룹**을 입력하고 **Enter** 키를 누릅니다.

2. **네트워크 보안 그룹** 블레이드에서 **+ 만들기**를 클릭합니다.

3. **네트워크 보안 그룹 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다. 

    |설정|값|
    |---|---|
    |구독|이 랩에서 사용 중인 Azure 구독의 이름|
    |Resource group|**AZ500LAB07**|
    |이름|**myNsg**|
    |지역|**미국 동부**|

4. **검토 + 만들기**를 클릭한 다음, **만들기**를 클릭합니다.

5. Azure Portal에서 **네트워크 보안 그룹** 블레이드로 돌아가 **myNsg** 항목을 클릭합니다.

6. **myNsg** 블레이드의 **설정** 섹션에서 **서브넷**을 클릭한 다음 **+ 연결**을 클릭합니다. 

7. **서브넷 연결** 블레이드에서 다음 설정을 지정하고 **확인**을 클릭합니다.

    |설정|값|
    |---|---|
    |가상 네트워크|**myVirtualNetwork**|
    |서브넷|**default**|

#### 작업 4: 웹 서버의 모든 트래픽 및 서버의 RDP에 인바운드 NSG 보안 규칙을 만듭니다. 

1. **myNsg** 블레이드의 **설정** 섹션에서 **인바운드 보안 규칙**을 클릭합니다.

2. 기본 인바운드 보안 규칙을 검토한 다음 **+ 추가**를 클릭합니다.

3. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정하여 TCP 포트 80 및 443을 **myAsgWebServers** 애플리케이션 보안 그룹에 허용합니다(다른 모든 값은 기본값으로 남겨 둡니다). 

    |설정|값|
    |---|---|
    |대상|드롭다운 목록에서 **애플리케이션 보안 그룹**을 선택한 다음 **myAsgWebServers**를 클릭합니다|
    |대상 포트 범위|**80,443**|
    |프로토콜|**TCP**|
    |우선 순위|**100**|                                                    
    |이름|**Allow-Web-All**|

4. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

5. **myNsg** 블레이드의 **설정** 섹션에서 **인바운드 보안 규칙**을 클릭한 다음 **+ 추가**를 클릭합니다.

6. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정하여 RDP 포트(TCP 3389)를 **myAsgMgmtServers** 애플리케이션 보안 그룹에 허용합니다(다른 모든 값은 기본값으로 남겨 둡니다). 

    |설정|값|
    |---|---|
    |대상|드롭다운 목록에서 **애플리케이션 보안 그룹**을 선택한 다음 **myAsgMgmtServers**를 클릭합니다.|
    |대상 포트 범위|**3389**|
    |프로토콜|**TCP**|
    |우선 순위|**110**|                                                    
    |이름|**Allow-RDP-All**|

7. **인바운드 보안 규칙 추가** 블레이드에서 **추가**를 클릭하여 새 인바운드 규칙을 만듭니다. 

> 결과: 가상 네트워크, 인바운드 보안 규칙을 가진 네트워크 보안 그리고 두개의 애플리케이션 보안 그룹을 배포하였습니다. 

### 연습 2: 가상 머신 배포 및 네트워크 필터 테스트

### 예상 소요 시간: 25분

이 연습에서는 다음 작업을 완료합니다.

- 작업 1: 웹 서버로 사용할 가상 머신을 만듭니다.
- 작업 2: 관리 서버로 사용할 가상 머신을 만듭니다. 
- 작업 3: 각 가상 머신 네트워크 인터페이스를 애플리케이션 보안 그룹에 연결합니다.
- 작업 4: 네트워크 트래픽 필터링을 테스트합니다.

#### 작업 1: 웹 서버로 사용할 가상 머신을 만듭니다.

이 작업에서는 웹 서버로 사용할 가상 머신을 만듭니다.

1. Azure Portal 페이지 위쪽의 **리소스, 서비스 및 문서 검색** 텍스트 상자에 **가상 머신**을 입력하고 **Enter** 키를 누릅니다.

2. **가상 머신** 블레이드에서 **+ 만들기**를 클릭하고 드롭다운 목록에서 **+ Azure 가상 머신**을 클릭합니다.

3. **가상 머신 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 사용).

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |Resource group|**AZ500LAB07**|
   |가상 머신 이름|**myVmWeb**|
   |지역|**(미국)미국 동부**|
   |이미지|**Windows Server 2022 Datacenter: Azure Edition- x64 Gen2**|
   |크기|**표준 D2s v3**|
   |사용자 이름|**학생**|
   |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
   |암호 확인|**암호 다시 입력**|
   |공용 인바운드 포트|**없음**|
   |기존 Windows Server 라이선스를 사용하시겠습니까? |**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

4. **다음: 디스크 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형**을 **표준 HDD**로 설정하고 **다음: 네트워킹 >** 을 선택합니다.

5. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 이전에 만든 네트워크인 **myVirtualNetwork**를 선택합니다.

6. **NIC 네트워크 보안 그룹** 아래에서 **없음**을 선택합니다.

7. **다음: 관리 >** 를 클릭한 다음 **가상 머신 만들기** 블레이드의 **모니터링** 탭에서 **다음: 모니터링 > 클릭하고 다음 설정을 확인합니다.

   |설정|값|
   |---|---|
   |부트 진단|**관리형 스토리지 계정으로 사용하도록 설정됨(권장)**|

8. **검토 + 만들기** 블레이드에서 **검토 + 만들기**를 클릭하고 유효성 검사가 성공하였는지 확인한 다음 **만들기**를 클릭합니다.

#### 작업 2: 관리 서버로 사용할 가상 머신을 만듭니다. 

이 작업에서는 관리 서버로 사용할 가상 머신을 만듭니다.

1. Azure Portal에서 **가상 머신** 블레이드로 다시 이동하고, **+ 만들기**를 클릭하고, 드롭다운 목록에서 **+ Azure 가상 머신**을 클릭합니다.

2. **가상 머신 만들기** 블레이드의 **기본 사항** 탭에서 다음 설정을 지정합니다(나머지는 기본값을 그대로 사용).

   |설정|값|
   |---|---|
   |구독|이 랩에서 사용할 Azure 구독의 이름|
   |Resource group|**AZ500LAB07**|
   |가상 머신 이름|**myVMMgmt**|
   |지역|(미국)미국 동부|
   |이미지|**Windows Server 2022 Datacenter: Azure Edition - x64 Gen2**|
   |크기|**표준 D2s v3**|
   |사용자 이름|**학생**|
   |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|
   |공용 인바운드 포트|**없음**|
   |이미 Windows Server 라이선스가 있으신가요?|**아니요**|

    >**참고**: 공용 인바운드 포트의 경우 미리 만들어진 NSG에 의존합니다. 

3. **다음: 디스크 >** 를 클릭하고, **가상 머신 만들기** 블레이드의 **디스크** 탭에서 **OS 디스크 유형**을 **표준 HDD**로 설정하고 **다음: 네트워킹 >** 을 선택합니다.

4. **가상 머신 만들기** 블레이드의 **네트워킹** 탭에서 이전에 만든 네트워크인 **myVirtualNetwork**를 선택합니다.

5. **NIC 네트워크 보안 그룹** 아래에서 **없음**을 선택합니다.

6. **다음: 관리 >** 를 클릭한 다음 **가상 머신 만들기** 블레이드의 모니터링 탭에서 **다음: **모니터링** > 클릭하고 다음 설정을 확인합니다.

   |설정|값|
   |---|---|
   |부트 진단|**관리형 스토리지 계정으로 사용하도록 설정됨(권장)**|

7. **검토 + 만들기** 블레이드에서 **검토 + 만들기**를 클릭하고 유효성 검사가 성공하였는지 확인한 다음 **만들기**를 클릭합니다.

    >**참고**: 가상 머신이 모두 프로비저닝될 때까지 기다린 후에 계속 진행합니다. 

#### 작업 3: 각 가상 머신 네트워크 인터페이스를 애플리케이션 보안 그룹에 연결

이 작업에서는 각 가상 머신 네트워크 인터페이스를 해당 애플리케이션 보안 그룹과 연결합니다. myVMWeb 가상 머신 인터페이스는 myAsgWebServers ASG에 연결됩니다. myVMMgmt 가상 머신 인터페이스는 myAsgMgmtServers ASG에 연결됩니다. 

1. Azure Portal에서 **가상 머신** 블레이드로 돌아가서 두 가상 머신 모두 **실행** 상태에 나열되었는지 확인합니다.

2. 가상 머신 목록에서 **myVMWeb** 항목을 클릭합니다.

3. **myVMWeb** 블레이드의 **설정** 섹션에서 **네트워킹**을 클릭한 다음 **myVMWeb \| 네트워킹** 블레이드에서 **애플리케이션 보안 그룹** 탭을 클릭합니다.

4. **애플리케이션 보안 그룹 구성**을 클릭하고 **애플리케이션 보안 그룹** 드롭다운 목록에서 **myAsgWebServer**를 선택한 다음 **저장**을 클릭합니다.

5. **가상 머신** 블레이드로 돌아가서 가상 머신 목록에서 **myVMMgmt** 항목을 클릭합니다.

6. **myVMMgmt** 블레이드의 **설정** 섹션에서 **네트워킹**을 클릭한 다음 **myVMMgmt \| 네트워킹** 블레이드에서 **애플리케이션 보안 그룹** 탭을 클릭합니다.

7. **애플리케이션 보안 그룹 구성**을 클릭하고 **애플리케이션 보안 그룹** 드롭다운 목록에서 **myAsgMgmtServers**를 선택한 다음 **저장**을 클릭합니다.

#### 작업 4: 네트워크 트래픽 필터링 테스트

이 작업에서는 네트워크 트래픽 필터를 테스트합니다. myVMMgmnt 가상 머신에 RDP를 할 수 있어야 합니다. 인터넷에서 myVMWeb 가상 머신으로 연결하고 기본 IIS 웹 페이지를 볼 수 있어야 합니다.  

1. **myVMMgmt** 가상 머신 블레이드로 돌아갑니다.

2. **myVMMgmt** 블레이드에서 **연결**을 클릭하고 드롭다운 메뉴에서 **RDP**를 클릭합니다. 

3. **RDP 파일 다운로드**를 클릭하고 원격 데스크톱을 통해 **myVMMgmt** Azure VM에 연결하는 데 사용합니다. 인증하라는 메시지가 표시되면 다음 자격 증명을 입력합니다.

   |설정|값|
   |---|---|
   |사용자 이름|**학생**|
   |암호|**랩 04 > 연습 1 > 작업 1 > 9단계에서 만든 개인 암호를 사용하세요.**|

    >**참고**: 원격 데스크톱 연결이 성공했는지 확인합니다. 이 시점에서 원격 데스크톱을 통해 myVMMgmt에 연결할 수 있다는 것을 확인하였습니다.

4. Azure Portal에서 **myVMWeb** 가상 머신 블레이드로 이동합니다.

5. **myVMWeb** 블레이드의 **운영** 섹션에서 **명령 실행**을 클릭한 다음 **RunPowerShellScript**를 클릭합니다.

6. **명령 스크립트 실행** 창에서 다음을 실행하여 **myVmWeb**에 웹 서버 역활을 설치합니다.

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**참고**: 설치가 완료될 때까지 기다립니다. 이 과정은 몇 분 정도 걸릴 수 있습니다. 이 시점에서 HTTP/HTTPS를 통해 myVMWeb에 액세스할 수 있는지 확인할 수 있습니다.

7. Azure Portal에서 **myVMWeb** 블레이드로 이동합니다.

8. **myVMWeb** 블레이드에서 myVmWeb Azure VM의 **공용 IP 주소**를 식별합니다.

9. 다른 브라우저 탭을 열고 이전 단계에서 식별한 IP 주소로 이동합니다.

    >**참고**: 포트 80은 **myAsgWebServers** 애플리케이션 보안 그룹의 설정에 따라 인터넷에서 인바운드가 허용되므로 기본 IIS 시작 페이지가 브라우저 페이지에 표시되어야 합니다. myVMWeb Azure VM의 네트워크 인터페이스는 해당 애플리케이션 보안 그룹과 연결됩니다. 

> 결과: NSG 및 ASG 구성이 작동하고 트래픽이 올바르게 관리되고 있는지 확인했습니다. 

**리소스 정리**

> 더 이상 사용하지 않는 새로 만든 Azure 리소스는 모두 제거하세요. 사용하지 않는 리소스를 제거하면 예상하지 못한 비용이 발생하지 않습니다. 

1. Azure Portal 오른쪽 상단에 있는 첫 번째 아이콘을 클릭하여 Cloud Shell을 엽니다. 메시지가 표시되면 **PowerShell** 및 **스토리지 만들기**를 선택합니다.

2. Cloud Shell 창의 왼쪽 위 모서리에 있는 드롭다운 메뉴에서 **PowerShell**이 선택되었는지 확인합니다.

3. Cloud Shell 창 내의 PowerShell 세션에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 제거합니다.
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  **Cloud Shell** 창을 닫습니다. 
