---
title: Azure AD 암호 보호 미리 보기 작업 및 보고
description: Azure AD 암호 보호 미리 보기 배포 후 작업 및 보고
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 14aa52b6d424423f4863efa63f3e2e66b84dac70
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163692"
---
# <a name="preview-azure-ad-password-protection-post-deployment"></a>미리 보기: Azure AD 암호 보호 배포 후

|     |
| --- |
| Azure AD 암호 보호 및 사용자 지정 금지 암호 목록은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

온-프레미스에 [Azure AD 암호 보호를 설치](howto-password-ban-bad-on-premises.md)한 후 Azure Portal에서 몇 가지 항목을 구성해야 합니다.

## <a name="configure-the-custom-banned-password-list"></a>사용자 지정 금지 암호 목록 구성

조직의 금지된 암호 목록을 사용자 지정하는 단계에 대한 [사용자 지정 금지 암호 목록 구성](howto-password-ban-bad.md) 문서의 지침을 따릅니다.

## <a name="enable-password-protection"></a>암호 보호 사용

1. [Azure Portal](https://portal.azure.com)에 로그인하여 **Azure Active Directory**, **인증 방법**으로 이동한 다음, **암호 보호(미리 보기)** 로 이동합니다.
1. **Windows Server Active Directory에서 암호 보호 사용**을 **예**로 설정합니다.
1. [배포 가이드](howto-password-ban-bad-on-premises.md#deployment-strategy)에 설명된 대로, 처음에는 **모드**를 **감사**로 설정하는 것이 좋습니다.
   * 기능에 익숙해진 후 **모드**를 **강제 적용**으로 전환하면 됩니다.
1. 페이지 맨 아래에 있는 **저장**

![Azure Portal에서 Azure AD 암호 보호 구성 요소를 사용하도록 설정](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>감사 모드

감사 모드는 "what if" 모드에서 소프트웨어를 실행하기 위한 방법으로 고안되었습니다. 각 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 현재 정책이 감사 모드에 포함되도록 구성된 경우 "잘못된" 암호는 이벤트 로그 메시지로 이어지지만 허용됩니다. 이것이 감사 모드와 강제 적용 모드의 유일한 차이점이며, 나머지 작업은 동일하게 실행됩니다.

> [!NOTE]
> 초기 배포 및 테스트는 항상 감사 모드로 시작하는 것이 좋습니다. 그 후 이벤트 로그의 이벤트를 모니터링하면서 강제 적용 모드를 사용하면 기존 운영 프로세스에 방해가 되는지 예상해 볼 수 있습니다.

## <a name="enforce-mode"></a>강제 적용 모드

강제 적용 모드는 최종 구성으로 고안되었습니다. 위에서 설명한 감사 모드와 마찬가지로, 각 DC 에이전트 서비스는 현재 활성 정책에 따라 들어오는 암호를 평가합니다. 강제 적용 모드를 사용하더라도 암호 정책에 따라 안전하지 않은 것으로 판단되는 암호는 거부됩니다.

강제 적용 모드에서 Azure AD 암호 보호 DC 에이전트에 의해 암호가 거부되면 기존 온-프레미스 암호 복잡성을 적용하여 암호가 거부될 때와 동일한 시각적 영향이 최종 사용자에게 발생합니다. 예를 들어 사용자의 로그온/암호 변경 화면에 다음과 같은 기존 오류 메시지가 표시될 수 있습니다.

"암호를 업데이트할 수 없습니다. 새 암호로 입력된 값이 도메인의 길이, 복잡성 또는 기록 요구 사항을 충족하지 않습니다."

이 메시지는 여러 가지 가능한 결과 중 한 가지 예일 뿐입니다. 안전하지 않은 암호를 설정하려고 시도하는 실제 소프트웨어 또는 시나리오에 따라 오류 메시지가 달라질 수 있습니다.

영향을 받는 최종 사용자는 IT 직원의 도움을 받아 새 요구 사항을 이해하면 안전한 암호를 선택해야 합니다.

## <a name="usage-reporting"></a>사용량 보고

`Get-AzureADPasswordProtectionSummaryReport` cmdlet을 사용하여 작업의 요약 보기를 생성할 수 있습니다. 이 cmdlet의 출력 예는 다음과 같습니다.

```
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

–Forest, -Domain 또는 –DomainController 매개 변수 중 하나를 사용하여 cmdlet의 보고 범위에 영향을 줄 수 있습니다. 매개 변수를 지정하지 않는 것은 –Forest를 의미합니다.

> [!NOTE]
> 이 cmdlet은 각 DC 에이전트 서비스의 관리 이벤트 로그를 원격으로 쿼리하는 방식으로 작동합니다. 이벤트 로그에 대량의 이벤트가 포함되어 있으면 cmdlet이 완료될 때까지 오래 걸릴 수 있습니다. 또한 대량 데이터 집합의 대량 네트워크 쿼리는 도메인 컨트롤러 성능에 영향을 줄 수 있습니다. 따라서 이 cmdlet은 프로덕션 환경에서 신중하게 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호에 대한 문제 해결 및 로깅 정보](howto-password-ban-bad-on-premises-troubleshoot.md)
