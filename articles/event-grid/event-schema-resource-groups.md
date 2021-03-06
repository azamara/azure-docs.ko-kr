---
title: Azure Event Grid 리소스 그룹 이벤트 스키마
description: Azure Event Grid를 사용하여 리소스 그룹 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: 407d9fd5b6f4d554af37b60edf12422f8816ac00
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495325"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid 리소스 그룹에 대한 이벤트 스키마

이 문서에서는 리소스 그룹 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

Azure 구독 및 리소스 그룹은 동일한 이벤트 유형을 내보냅니다. 이벤트 유형은 리소스의 변경과 관련됩니다. 주요 차이는 리소스 그룹이 리소스 그룹 내 리소스에 대한 이벤트를 내보내고 Azure 구독은 구독 전체에서 리소스에 대한 이벤트를 내보낸다는 것입니다.

`management.azure.com`로 전송되는 PUT, PATCH, DELETE 작업에 대해 리소스 이벤트를 만듭니다. POST 및 GET 작업은 이벤트를 만들지 않습니다. 데이터 평면에 전송된 작업(`myaccount.blob.core.windows.net`과 같은)은 이벤트를 만들지 않습니다.

리소스 그룹에 대한 이벤트를 구독하면 엔드포인트는 해당 리소스 그룹에 대한 모든 이벤트를 받습니다. 이 이벤트에는 가상 머신 업데이트 같이 확인하려는 이벤트뿐만 아니라 배포 기록에 새 항목 작성 같이 중요하지 않을 수 있는 이벤트도 포함될 수 있습니다. 엔드포인트에서 모든 이벤트를 수신하고 처리하려는 이벤트를 처리하는 코드를 작성하거나 이벤트 구독을 만들 때 필터를 설정할 수 있습니다.

프로그래밍 방식으로 이벤트를 처리하려면 `operationName` 값을 검토해 이벤트를 정렬할 수 있습니다. 예를 들어 이벤트 엔드포인트는 `Microsoft.Compute/virtualMachines/write` 또는 `Microsoft.Storage/storageAccounts/write`와 동일한 작업에 대한 이벤트만 처리할 수 있습니다.

이벤트 주체는 작업의 대상이 되는 리소스의 리소스 ID입니다. 리소스에 대한 이벤트를 필터링하려면 이벤트 구독을 만들 때 해당 리소스 ID를 제공합니다. 샘플 스크립트는 [리소스 그룹에 대한 구독 및 필터링 - PowerShell](scripts/event-grid-powershell-resource-group-filter.md) 또는 [리소스 그룹에 대한 구독 및 필터링 - Azure CLI](scripts/event-grid-cli-resource-group-filter.md)를 참조하세요. 리소스 종류별로 필터링하려면 `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`과 같은 형식으로 값을 사용합니다.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

VM을 만들거나 저장소 계정을 삭제할 때와 같이 리소스 그룹은 Azure Resource Manager에서 관리 이벤트를 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | 리소스 생성 또는 업데이트 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceWriteFailure | 리소스 생성 또는 업데이트 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceWriteCancel | 리소스 생성 또는 업데이트 작업이 취소되면 발생합니다. |
| Microsoft.Resources.ResourceDeleteSuccess | 리소스 삭제 작업이 성공하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteFailure | 리소스 삭제 작업이 실패하면 발생합니다. |
| Microsoft.Resources.ResourceDeleteCancel | 리소스 삭제 작업이 취소되면 발생합니다. 이 이벤트는 템플릿 배포가 취소될 때 발생합니다. |

## <a name="example-event"></a>예제 이벤트

다음 예제는 **ResourceWriteSuccess** 이벤트의 스키마를 보여줍니다. 동일한 스키마가 `eventType`에 대한 다른 값을 사용하여 **ResourceWriteFailure** 및 **ResourceWriteCancel** 이벤트에 사용됩니다.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

다음 예제는 **ResourceDeleteSuccess** 이벤트의 스키마를 보여줍니다. 동일한 스키마가 `eventType`에 대한 다른 값을 사용하여 **ResourceDeleteFailure** 및 **ResourceDeleteCancel** 이벤트에 사용됩니다.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 리소스 그룹 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 권한 부여 | object | 작업에 대해 요청된 권한입니다. |
| claims | object | 클레임의 속성입니다. 자세한 내용은 [JWT 사양](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)을 참조하세요. |
| CorrelationId | string | 문제 해결을 위한 작업 ID입니다. |
| httpRequest | object | 작업의 세부 정보입니다. 이 개체는 기존 리소스를 업데이트하거나 리소스를 삭제하는 경우에만 포함됩니다. |
| resourceProvider | string | 작업을 수행하는 리소스 공급자입니다. |
| resourceUri | string | 작업에서 리소스의 URI입니다. |
| operationName | string | 수행된 작업입니다. |
| status | string | 작업의 상태. |
| subscriptionId | string | 리소스의 구독 ID입니다. |
| tenantId | string | 리소스의 테넌트 ID입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
