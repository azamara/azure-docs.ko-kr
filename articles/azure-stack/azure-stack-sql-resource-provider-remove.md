---
title: Azure 스택 SQL 리소스 공급자 제거 | Microsoft Docs
description: 자세한 내용은 방법 Azure 스택 배포에서 SQL 리소스 공급자를 제거할 수 있습니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b73deebb10d0c81a06df9cd192eaa2ef28de744d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083043"
---
# <a name="remove-the-sql-resource-provider"></a>SQL 리소스 공급자를 제거 합니다.

SQL 리소스 공급자를 제거 하기 전에 모든 공급자 종속성을 제거 해야 합니다. 또한는 리소스 공급자를 설치 하는 데 사용 된 배포 패키지의 복사본을 해야 합니다.

일부의 정리 작업을 실행 하기 전에 작업을 수행 하는 _DeploySqlProvider.ps1_ 리소스 공급자를 제거 하는 스크립트입니다.
테 넌 트 다음 정리 작업에 대 한 책임이 있습니다.

* 리소스 공급자에서 모든 데이터베이스를 삭제 합니다. (삭제 테 넌 트 데이터베이스 하지 않는 데이터는 삭제 됩니다.)
* 리소스 공급자 네임 스페이스에서 등록을 취소 합니다.

관리자는 다음 정리 작업을 담당 합니다.

* SQL 리소스 공급자에서 호스팅 서버를 삭제합니다.
* SQL 리소스 공급자를 참조 하는 모든 계획을 삭제 합니다.
* SQL 리소스 공급자와 관련 된 할당량을 삭제 합니다.

## <a name="to-remove-the-sql-resource-provider"></a>SQL 리소스 공급자를 제거 하려면

1. 모든 기존 SQL 리소스 공급자 종속성을 제거를 확인 합니다.

   > [!NOTE]
   > SQL 리소스 공급자를 제거 하면 종속 리소스에서 리소스 공급자를 현재 사용 중인 경우에 진행 됩니다.
  
2. SQL 리소스 공급자의 복사본을 이진 가져오고 후 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기 실행 합니다.

3. 관리자 권한으로 새 PowerShell 콘솔 창을 열고 SQL 리소스 공급자 이진 파일의 압축을 푼 디렉터리로 변경 합니다.

4. 다음 매개 변수를 사용 하 여 DeploySqlProvider.ps1 스크립트를 실행 합니다.

    * **제거**합니다. 리소스 공급자와 관련 된 모든 리소스를 제거합니다.
    * **PrivilegedEndpoint**합니다. IP 주소 또는 권한 있는 끝점의 DNS 이름입니다.
    * **CloudAdminCredential**합니다. 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다.
    * **AzCredential**합니다. Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다.

## <a name="next-steps"></a>다음 단계

[PaaS로 응용 프로그램 서비스를 제공 합니다.](azure-stack-app-service-overview.md)
