---
title: Microsoft Azure 스택 문제 해결 | Microsoft Docs
description: Azure 스택 개발 키트 (ASDK) 문제 해결 정보입니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6c715f07f75c9196b7cf2cc8659c6e541e1260da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30167903"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Microsoft Azure 스택 개발 키트 (ASDK) 문제 해결
이 문서는 ASDK에 대 한 일반적인 문제 해결 정보를 제공합니다. 문서화 되지 않은 문제가 발생 하는 경우을 선택 했는지 확인는 [Azure 스택 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) 정보 및 추가 지원이 필요 합니다.  

> [!IMPORTANT]
> ASDK 평가 환경 이기 때문에 지원은 없습니다 공식를 통해 Microsoft 지원 CSS (고객 서비스)를 제공 합니다.

이 섹션에 설명 된 문제 해결을 위한 권장 사항 여러 원본에서 파생 된 및 그렇지 해당 하는 문제를 해결할 수 있습니다. 코드 예제는 "있는 그대로" 제공 됩니다 및 예상된 결과 보장할 수 없습니다. 이 섹션은 제품을 향상 되는 대로 자주 편집 하 고 업데이트 적용 됩니다.

## <a name="deployment"></a>배포
### <a name="deployment-failure"></a>배포 실패
설치 중에 오류가 발생 하면 다시 시작할 수 있습니다 실패 한 단계에서 배포를 사용 하 여 다음 예제와 같이 배포 스크립트의 다시 실행된-옵션:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>배포 후에 PowerShell 세션 계속 열려 및 모든 출력은 표시 되지 않습니다.
이 문제를 선택한 경우에 PowerShell 명령 창의 기본 동작 결과 때문일 수 있습니다. 개발 키트 배포 성공에 있지만 스크립트 창을 선택할 때 일시 중지 되었습니다. 명령 창의 제목 표시줄에 "select" 이라는 단어를 검색 하 여 설치를 완료 하는 것을 확인할 수 있습니다. 선택 취소 하려면 ESC 키를 누릅니다 하 고 그 뒤 완료 메시지를 표시 합니다.

## <a name="virtual-machines"></a>가상 머신
### <a name="default-image-and-gallery-item"></a>기본 이미지와 갤러리 항목
Windows Server 이미지와 갤러리 항목을 스택에서 Azure Vm을 배포 하기 전에 추가 되어야 합니다.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>내 Azure 스택 호스트를 다시 시작한 후 일부 Vm 자동으로 시작할 수 없습니다.
호스트를 다시 부팅 한 후 Azure 스택 서비스를 즉시 사용할 수 없는 경우 발생할 수 있습니다. Azure 스택 때문에 이것이 [인프라 Vm](asdk-architecture.md#virtual-machine-roles) 및 RPs take 일부 일관성을 확인 하기에 충분 하지만 결국 자동으로 시작 됩니다.

해당 테 넌 트 Vm이 Azure 스택 개발 키트 호스트를 다시 부팅 한 후 자동으로 시작 하지 않는 경우도 있습니다. 이것은 알려진된 문제 이며만 온라인 상태로 전환 하는 몇 가지 수동 단계 필요 합니다.

1.  Azure 스택 개발 키트 호스트에서 시작 **장애 조치 클러스터 관리자** 시작 메뉴에서 합니다.
2.  클러스터 선택 **S Cluster.azurestack.local**합니다.
3.  **역할**을 선택합니다.
4.  테 넌 트 Vm에 표시 된 *저장* 상태입니다. 모든 인프라 Vm을 실행 하는 테 넌 트 Vm을 마우스 오른쪽 단추로 클릭 하 고 선택 **시작** VM을 다시 시작할 수 있습니다.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>일부 가상 컴퓨터를 삭제 한 했지만 아직 디스크에 VHD 파일을 참조 하십시오. 이 동작이 예상
예, 예상 되는 동작입니다. 때문에 이러한 방식으로 설계 된:

* VM을 삭제 하는 경우 Vhd는 삭제 되지 않습니다. 디스크는 리소스 그룹에 별도 리소스입니다.
* 저장소 계정을 삭제 되는 삭제 하는 즉시 Azure 리소스 관리자를 통해 표시 되지만 가비지 수집 실행 될 때까지 포함 될 수 있습니다 디스크 저장소에 보관 계속 됩니다.

"마지막 줄" Vhd를 참조 하는 경우에 삭제 된 저장소 계정에 대 한 폴더의 일부 인지 확인 해야 합니다. 저장소 계정을 삭제 되지 않은 경우 정상적인는 여전히 남아 있습니다.

자세한 내용은에서 보존 임계값 및 요청 시 다시 구성 하는 방법에 대 한 [저장소 계정을 관리](.\.\azure-stack-manage-storage-accounts.md)합니다.

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>저장소 확보
포털에 표시 하려면 회수 된 용량에 대 한 최대 14 시간까지 걸릴 수 있습니다. 공간 확보는 블록 blob 저장소에 내부 컨테이너 파일 사용량 비율이 비롯 한 다양 한 요인에 따라 달라 집니다. 따라서 얼마나 많은 데이터가 삭제 됩니다에 따라 보장은 없습니다 가비지 수집기를 실행 하는 경우 회수 될 수 있는 공간의 크기입니다.

## <a name="next-steps"></a>다음 단계
[Azure 스택 지원 포럼 방문](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

