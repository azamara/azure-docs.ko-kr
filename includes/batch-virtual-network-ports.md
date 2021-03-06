- VNet은 Batch 계정과 동일한 Azure **지역** 및 **구독**에 있어야 합니다.

- 가상 머신 구성을 사용하여 만든 풀에는 ARM(Azure Resource Manager) 기반 VNet만 사용할 수 있습니다. 클라우드 서비스 구성을 사용하여 만든 풀에는 클래식 VNet만 사용할 수 있습니다.
  
- `MicrosoftAzureBatch` 서비스 주체에는 지정된 VNet에 대한 `Classic Virtual Machine Contributor` RBAC(역할 기반 Access Control) 역할이 있어야 합니다. Azure Resource Manager 기반 VNet을 사용하려면 VNet에 액세스하고 VM을 서브넷에 배포할 수 있는 권한이 있어야 합니다.

- 풀에 대해 지정한 서브넷에는 풀에 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 즉 풀의 `targetDedicatedNodes` 및 `targetLowPriorityNodes` 속성 합계입니다. 서브넷에 할당되지 않은 IP 주소가 충분하지 않으면 풀은 계산 노드를 부분적으로 할당하고 크기 조정 오류를 반환합니다. 

- Azure VNet에 배포된 가상 머신 구성의 풀은 추가 Azure 네트워킹 리소스를 자동으로 할당합니다. VNet의 각 50 풀 노드에 대해 네트워크 보안 그룹 1개, 공용 IP 주소 1개 및 부하 분산 장치 1개 등의 리소스가 필요합니다. 이러한 리소스는 Batch 풀을 만들 때 제공되는 가상 네트워크가 포함된 구독의 [할당량](../articles/batch/batch-quota-limit.md)에 의해 제한됩니다.

- VNet은 Batch 서비스의 통신이 계산 노드에서 태스크를 예약할 수 있도록 허용해야 합니다. VNet에 연결된 NSG(네트워크 보안 그룹)가 있는 경우 검사를 통해 확인할 수 있습니다. 지정된 서브넷에서 계산 노드와의 통신을 NSG에서 거부한 경우 Batch 서비스는 계산 노드의 상태를 **사용할 수 없음**으로 설정합니다. 

- 지정된 VNet에 연결된 NSG(네트워크 보안 그룹) 및/또는 방화벽이 있는 경우 다음 표에서처럼 인바운드 및 아웃바운드 포트를 구성합니다.


  |    대상 포트    |    원본 IP 주소      |   원본 포트    |    Batch가 NSG를 추가합니까?    |    VM을 사용할 수 있도록 하는 데 필요합니까?    |    사용자의 작업   |
  |---------------------------|---------------------------|----------------------------|----------------------------|-------------------------------------|-----------------------|
  |   <ul><li>가상 머신 구성을 사용하여 만든 풀의 경우: 29876, 29877</li><li>클라우드 서비스 구성을 사용하여 만든 풀의 경우: 10100, 20100, 30100</li></ul>        |    * 또는 추가 보안을 위해 Batch 서비스의 IP 주소를 지정합니다. Batch 서비스의 IP 주소 목록을 가져오려면 Azure 지원에 문의하세요. | * 또는 443 |    예. Batch는 VM에 연결된 네트워크 인터페이스(NIC)의 수준에서 NSG를 추가합니다. 이러한 NSG는 Batch 서비스 역할 IP 주소의 트래픽만 허용합니다. 이러한 포트에 전체 웹을 허용하더라도 NIC에서 트래픽이 차단됩니다. |    yes  |  Batch는 Batch IP 주소만 허용하기 때문에 NSG를 지정할 필요가 없습니다. <br /><br /> 하지만 NSG를 지정하는 경우에는 이러한 포트에 인바운드 트래픽을 허용하세요. <br /><br /> NSG에서 *를 원본 IP로 지정하더라도 Batch는 VM에 연결된 NIC 수준에서 NSG를 추가합니다. |
  |    3389(Windows), 22(Linux)               |    VM에 원격으로 액세스할 수 있도록 디버깅용으로 사용되는 사용자 컴퓨터입니다.    |   *  | 아니요                                    |    아니요                    |    VM에 원격 액세스(RDP 또는 SSH)를 허용하려는 경우 NSG를 추가합니다.   |                                


  |    아웃바운드 포트    |    대상    |    Batch가 NSG를 추가합니까?    |    VM을 사용할 수 있도록 하는 데 필요합니까?    |    사용자의 작업    |
  |------------------------|-------------------|----------------------------|-------------------------------------|------------------------|
  |    443    |    Azure Storage    |    아니요    |    yes    |    NSG를 추가하는 경우에는 이 포트에 아웃바운드 트래픽을 허용하세요.    |

   또한 VNet을 제공하는 모든 사용자 지정 DNS 서버에서 Azure Storage 끝점을 확인할 수 있어야 합니다. 특히 `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` 및 `<account>.blob.core.windows.net` 양식의 URL을 확인할 수 있어야 합니다. 

   리소스 관리자 기반 NSG를 추가한 경우 [서비스 태그](../articles/virtual-network/security-overview.md#service-tags)를 사용하여 아웃바운드 연결의 특정 지역에 대한 저장소 IP 주소를 선택할 수 있습니다. 저장소 IP 주소는 배치 계정 및 VNet과 동일한 지역이어야 합니다. 서비스 태그는 현재 선택한 Azure 지역에서는 미리 보기 중입니다.