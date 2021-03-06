---
title: Azure Functions를 사용하여 Azure Logic Apps에서 사용자 지정 코드 추가 및 실행 | Microsoft Docs
description: Azure Functions를 사용하여 Azure Logic Apps에서 사용자 지정 코드 조각을 추가 및 실행하는 방법 알아보기
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263193"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Azure Functions를 사용하여 Azure Logic Apps에서 사용자 지정 코드 조각을 추가 및 실행

논리 앱에서 특정 문제를 해결하는 충분한 코드만 만들고 실행하려는 경우 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 자체 함수를 만들 수 있습니다. 이 서비스는 전체 앱을 생성하지 않고, 코드 실행을 위한 인프라 없이도 논리 앱에서 Node.js 또는 C#으로 작성된 사용자 지정 코드 조각을 만들고 실행하기 위한 기능을 제공합니다. Azure Functions는 클라우드에서 서버리스 컴퓨팅을 제공하며, 다음 예제와 같은 작업을 수행하는 데 유용합니다.

* Node.js 또는 C#에서 지원되는 함수를 사용하여 논리 앱의 동작을 확장합니다.
* 논리 앱 워크플로에서 계산을 수행합니다.
* 논리 앱에서 고급 서식 지정 또는 계산 필드를 적용합니다.

[Azure Functions에서 논리 앱을 호출](#call-logic-app)할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 따르려면 다음과 같은 항목이 필요합니다.

* 아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* 함수를 추가하려는 논리 앱

  논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  함수를 실행하기 위한 작업을 추가하려면 먼저 논리 앱이 트리거로 시작되어야 합니다.

* Azure Functions 및 Azure 함수에 대한 컨테이너인 Azure 함수 앱. 함수 앱이 없는 경우 [함수 앱을 먼저 만들어야 합니다](../azure-functions/functions-create-first-azure-function.md). 그런 후 [논리 앱과는 별도로](#create-function-external) 또는 Logic Apps 디자이너의 [논리 앱 내에서](#create-function-designer) 함수를 만들 수 있습니다.

  신규 및 기존 Azure 함수 앱 및 함수에는 논리 앱을 사용할 때 동일한 요구 사항이 적용됩니다.

  * 함수 앱은 논리 앱과 동일한 Azure 구독에 속해야 합니다.

  * 함수는 **JavaScript** 또는 **C#** 에 대해 **제네릭 웹후크** 함수 템플릿을 사용해야 합니다. 이 템플릿은 논리 앱의 `application/json` 형식을 갖는 콘텐츠를 수락할 수 있습니다. 또한 이러한 템플릿은 이러한 템플릿으로 만든 사용자 지정 함수를 논리 앱에 추가할 때 Logic Apps 디자이너가 해당 함수를 찾아 표시하는 데도 도움이 됩니다.

  * 함수 템플릿의 **모드** 속성이 **웹후크**로 설정되어 있고 **웹후크 형식** 속성이 **제네릭 JSON**으로 설정되어 있는지 확인합니다.

    1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.
    2. 기본 Azure 메뉴에서 **함수 앱**을 선택합니다. 
    3. **함수 앱** 목록에서 함수 앱을 선택하고 함수를 확장한 후 **통합**을 선택합니다. 
    4. 템플릿의 **모드** 속성이 **웹후크**로 설정되어 있고 **웹후크 형식** 속성이 **제네릭 JSON**으로 설정되어 있는지 확인합니다. 

  * 이전에 [Swagger 파일](http://swagger.io/)로 알려져 있던 [API 정의](../azure-functions/functions-openapi-definition.md)가 함수에 포함된 경우 Logic Apps 디자이너는 함수 매개 변수를 사용하기 위한 보다 풍부한 환경을 제공합니다. 
  논리 앱이 Swagger 설명이 포함된 함수를 찾아 액세스할 수 있도록 [먼저 다음 단계를 수행하여 함수 앱을 설정](#function-swagger)합니다.

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>논리 앱 외부에 함수 만들기

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 논리 앱과 동일한 Azure 구독이 있어야 하는 Azure 함수 앱을 만든 후 Azure 함수를 만듭니다. Azure Functions를 처음 접하는 경우 추가할 수 있고 논리 앱에서 호출할 수 있는 Azure 함수를 만들기 위한 다음 요구 사항에 유의하면서 [Azure Portal에서 첫 번째 함수를 만드는](../azure-functions/functions-create-first-azure-function.md) 방법을 알아봅니다.

* **JavaScript** 또는 **C#** 둘 다에 대해 **제네릭 웹후크** 함수를 선택해야 합니다.

  ![제네릭 웹후크 - JavaScript 또는 C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Azure 함수를 만든 후에 템플릿의 **모드** 및 **웹후크 형식** 속성이 올바르게 설정되어 있는지 확인합니다.

  1. **함수 앱** 목록에서 함수를 확장하고 **통합**을 선택합니다. 

  2. 템플릿의 **모드** 속성이 **웹후크**로 설정되어 있고 **웹후크 형식** 속성이 **제네릭 JSON**으로 설정되어 있는지 확인합니다. 

     ![함수 템플릿의 "통합" 속성](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* 필요에 따라, 이전에 [Swagger 파일](http://swagger.io/)로 알려져 있던 [API 정의를 생성](../azure-functions/functions-openapi-definition.md)하는 경우 Logic Apps 디자이너에서 함수 매개 변수를 사용하기 위한 보다 풍부한 환경을 누릴 수 있습니다. 논리 앱이 Swagger 설명이 포함된 함수를 찾아 액세스할 수 있도록 함수 앱을 설정하려면 다음을 수행합니다.

  * 함수 앱이 현재 실행되고 있는지 확인합니다.

  * 함수 앱에서 모든 원본이 허용되도록 [CCORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)를 설정합니다.

    1. **함수 앱** 목록에서 함수 앱 > **플랫폼 기능** > **CORS**를 차례로 선택합니다.

       ![함수 앱 > "플랫폼 기능" > "CORS" 선택](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. **CORS** 아래에 `*` 와일드카드 문자를 추가하되, 목록의 다른 원본은 모두 제거하고 **저장**을 선택합니다.

       ![함수 앱 > "플랫폼 기능" > "CORS" 선택](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>HTTP 요청 내에서 속성 값에 액세스

웹후크 함수는 HTTP 요청을 입력으로 수락하고 해당 요청을 다른 함수에 전달할 수 있습니다. 예를 들어, Logic Apps에는 [DateTime 값을 변환하는 함수](../logic-apps/workflow-definition-language-functions-reference.md)가 있지만 이 기본 샘플 JavaScript 함수는 함수에 전달되는 요청 개체 내의 속성에 액세스하고 해당 속성 값에 대해 작업을 수행하는 방법을 보여 줍니다. 개체 내의 속성에 액세스하기 위해 이 예제에서는 [점(.) 연산자](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)를 사용합니다. 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

이 함수 내에서 수행되는 작업은 다음과 같습니다.

1. 이 함수는 `data` 변수를 만들고 `request` 개체 내의 `body` 개체를 해당 변수에 할당합니다. 이 함수는 점(.) 연산자를 사용하여 `request` 개체 내의 `body` 개체를 참조합니다. 

   ```javascript
   var data = request.body;
   ```

2. 이 함수는 이제 `data` 변수를 통해 `date` 속성에 액세스하고, `ToDateString()` 함수를 호출하여 해당 속성 값을 DateTime 형식에서 DateString 형식으로 변환할 수 있습니다. 또한 이 함수는 다음과 같이 함수의 응답에서 `body` 속성을 통해 결과를 반환합니다. 

   ```javascript
   body: data.date.ToDateString();
   ```

지금까지 Azure 함수를 만들었으므로 [논리 앱에 함수를 추가](#add-function-logic-app)하는 방법에 대한 단계를 따르세요.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>논리 앱 내에서 함수 만들기

Logic Apps 디자이너의 논리 앱 내에서 시작하는 Azure 함수를 만들려면 먼저 함수에 대한 컨테이너에 해당하는 Azure 함수 앱이 있어야 합니다. 함수 앱이 없는 경우 함수 앱을 먼저 만듭니다. [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function.md)를 참조하세요. 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 Logic Apps 디자이너에서 논리 앱을 엽니다. 

2. 함수를 만들어 추가하려는 단계에서 **새 단계** > **작업 추가**를 선택합니다. 

3. 검색 상자에서 "azure functions"를 필터로 입력합니다.
작업 목록에서 **Azure 함수 선택 - Azure Functions** 작업을 선택합니다. 

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 함수 앱 목록에서 함수 앱을 선택합니다. 작업 목록이 열리면 **Azure Functions - 새 함수 만들기** 작업을 선택합니다.

   ![함수 앱 선택](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. 함수 정의 편집기에서 함수를 정의합니다.

   1. **함수 이름** 상자에 함수에 대한 이름을 입력합니다. 

   2. **코드** 상자에 함수 실행이 완료된 후에 논리 앱에 반환하려는 응답 및 페이로드를 포함하여 함수 코드를 템플릿에 추가합니다. 
   템플릿 코드의 컨텍스트 개체는 논리 앱이 함수에 전달하는 메시지 및 콘텐츠를 설명합니다.

      ![함수 정의](./media/logic-apps-azure-functions/function-definition.png)

      함수 내에서 이 구문을 사용하여 컨텍스트 개체의 속성을 참조할 수 있습니다.

      ```text
      context.<token-name>.<property-name>
      ```
      이 예제에서 사용하게 될 구문은 다음과 같습니다.

      ```text
      context.body.content
      ```

   3. 작업을 완료하면 **만들기**를 선택합니다.

6. **요청 본문** 상자에 함수 입력으로 전달할 컨텍스트 개체를 지정합니다. 이 개체는 JSON (JavaScript Notation)으로 형식이 지정되어야 합니다. **요청 본문** 상자를 클릭하면 이전 단계에서 사용할 수 있는 속성에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 열립니다. 

   이 예제에서는 이메일 트리거에서 **Body** 토큰에 해당 개체를 전달합니다.  

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   컨텍스트 개체의 내용에 따라, Logic Apps 디자이너는 사용자가 인라인으로 편집할 수 있는 함수 템플릿을 생성합니다. 
   또한 Logic Apps는 입력 컨텍스트 개체를 기준으로 변수를 만듭니다.

   이 예제에서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 
   그러나 개체가 문자열, JSON 개체 또는 JSON 배열이어야 하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 
   컨텍스트 개체를 문자열로 캐스팅하려면 다음 예제와 같이 큰따옴표를 추가합니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. 사용할 메서드, 요청 헤더 또는 쿼리 매개 변수와 같은 기타 세부 정보를 지정하려면 **고급 옵션 표시**를 선택합니다.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>논리 앱에 기존 함수 추가

논리 앱에서 기존 Azure 함수를 호출하려면 Logic Apps 디자이너에서 다른 작업처럼 Azure 함수를 추가할 수 있습니다. 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 Logic Apps 디자이너에서 논리 앱을 엽니다. 

2. 함수를 추가하려는 단계에서 **새 단계** > **작업 추가**를 선택합니다. 

3. 검색 상자에서 "azure functions"를 필터로 입력합니다.
작업 목록에서 **Azure 함수 선택 - Azure Functions** 작업을 선택합니다. 

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. 함수 앱 목록에서 함수 앱을 선택합니다. 함수 목록이 표시되면 함수를 선택합니다. 

   ![함수 앱 및 Azure 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 정의(Swagger 설명)가 있고 [논리 앱이 해당 함수를 찾아 액세스할 수 있도록 설정된](#function-swagger) 함수에 대해 **Swagger 작업**을 선택할 수 있습니다.

   ![함수 앱, "Swagger 작업" 및 Azure 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. **요청 본문** 상자에 함수 입력으로 전달할 컨텍스트 개체를 지정합니다. 이 개체는 JSON (JavaScript Notation)으로 형식이 지정되어야 합니다. 이 컨텍스트 개체는 논리 앱이 함수에 보내는 메시지 및 콘텐츠를 설명합니다. 

   **요청 본문** 상자를 클릭하면 이전 단계에서 사용할 수 있는 속성에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 열립니다. 
   이 예제에서는 이메일 트리거에서 **Body** 토큰에 해당 개체를 전달합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   이 예제에서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 
   그러나 개체가 문자열, JSON 개체 또는 JSON 배열이어야 하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 
   컨텍스트 개체를 문자열로 캐스팅하려면 다음 예제와 같이 큰따옴표를 추가합니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. 사용할 메서드, 요청 헤더 또는 쿼리 매개 변수와 같은 기타 세부 정보를 지정하려면 **고급 옵션 표시**를 선택합니다.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>함수에서 논리 앱 호출

Azure 함수 내에서 논리 앱을 트리거하려면 해당 논리 앱에 호출 가능한 끝점 또는 보다 구체적으로 말하면 **Request** 트리거가 있어야 합니다. 그런 다음, 함수 내에서 해당 **Request** 트리거에 대한 URL에 HTTP POST 요청을 전송하고 해당 논리 앱에서 처리할 페이로드를 포함합니다. 자세한 내용은 [논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. 

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
