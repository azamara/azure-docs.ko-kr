---
title: 정규식과 일치하는 데이터를 가져오는 LUIS 앱을 만드는 자습서 - Azure | Microsoft Docs
description: 이 자습서에서는 의도를 사용하는 간단한 LUIS 앱 및 데이터를 추출하는 정규식 엔터티를 만드는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 994bd6f2a041e25d15c7e0b4a216952cec4101fa
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492826"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>자습서: 3. 정규식 엔터티 추가
이 자습서에서는 **정규식** 엔터티를 사용하여 발화에서 형식이 일관되게 지정된 데이터를 추출하는 방법을 보여 주는 앱을 만듭니다.


<!-- green checkmark -->
> [!div class="checklist"]
> * 정규식 엔터티에 대한 이해 
> * FindForm 의도를 사용하여 HR(인사 관리) 도메인에 대한 LUIS 앱 사용
> * 발화에서 양식 번호를 추출하는 정규식 엔터티 추가
> * 앱 학습 및 게시
> * 앱의 엔드포인트를 쿼리하여 LUIS JSON 응답 확인

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
[미리 작성된 엔터티](luis-tutorial-prebuilt-intents-entities.md) 자습서의 인사 관리 앱이 없으면 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json) Github 리포지토리의 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app).

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `regex`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 


## <a name="purpose-of-the-regular-expression-entity"></a>정규식 엔터티의 목적
엔터티의 목적은 발화에 포함된 중요한 데이터를 가져오는 것입니다. 앱에서 정규식 엔터티를 사용하는 것은 발화에서 형식이 지정된 HR(인사 관리) 양식 번호를 추출하는 것이며, 기계 학습이 아닙니다. 

간단한 발화 예제는 다음과 같습니다.

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

발화의 약어 또는 속어 버전은 다음과 같습니다.

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
양식 번호와 일치하는 정규식 엔터티는 `hrf-[0-9]{6}`입니다. 이 정규식은 `hrf -` 리터럴 문자와 일치하지만 대/소문자 및 문화권 변형을 무시합니다. 6자리 숫자에 대해 0에서 9까지의 숫자와 정확하게 일치합니다.

HRF는 인사 관리 양식을 나타냅니다.

### <a name="tokenization-with-hyphens"></a>하이픈으로 토큰화
발화가 의도에 추가되면 LUIS에서 해당 발화를 토큰화합니다. 이러한 발화에 대한 토큰화에서는 하이픈의 앞뒤에 공백이 추가됩니다(예: `Where is HRF - 123456?`). 정규식은 토큰화되기 전에 원시 양식으로 발화에 적용됩니다. _원시_ 양식에 적용되므로 정규식에서 단어 경계를 처리할 필요가 없습니다. 


## <a name="add-findform-intent"></a>FindForm 의도 추가

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

2. **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 `FindForm`을 입력하고 **완료**를 선택합니다. 

    ![검색 상자에서 유틸리티가 입력된 새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. 의도에 발화 예제를 추가합니다.

    |발화 예제|
    |--|
    |hrf-123456에 대한 URL은 무엇인가요?|
    |hrf-345678은 어디에 있나요?|
    |hrf-456098은 언제 업데이트되었나요?|
    |지난주 John Smith가 hrf-234639를 업데이트했나요?|
    |hrf-345123 버전의 수는 몇 개인가요?|
    |hrf-123456 양식에 대한 권한을 부여해야 하는 사람은 누구인가요?|
    |hrf-345678에서 로그오프해야 사용자의 수는 얼마인가요?|
    |hrf-234123의 날짜는?|
    |hrf-546234의 작성자는?|
    |hrf-456234의 제목은?|

    [ ![새 발화가 강조 표시된 의도 페이지의 스크린샷](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    응용 프로그램에는 이전 자습서에서 추가된 미리 작성된 번호 엔터티가 있으므로 각 양식 번호에 태그가 지정됩니다. 이는 클라이언트 응용 프로그램에 대해 충분하지만 번호에 숫자 형식의 레이블이 지정되지 않습니다. 적절한 이름으로 새 엔터티를 만들면 LUIS에서 반환할 때 클라이언트 응용 프로그램에서 해당 엔터티를 적절히 처리할 수 있습니다.

## <a name="create-an-hrf-number-regular-expression-entity"></a>HRF 번호 정규식 엔터티 만들기 
다음 단계에서는 정규식 엔터티를 만들어 LUIS에 HRF 번호 형식을 알려줍니다.

1. 왼쪽 패널에서 **엔터티**를 선택합니다.

2. [엔터티] 페이지에서 **새 엔터티 만들기** 단추를 선택합니다. 

3. 팝업 대화 상자에서 `HRF-number`라는 새 엔터티 이름을 입력하고, 엔터티 형식으로 **RegEx**를 선택하고, 정규식으로 `hrf-[0-9]{6}`을 입력한 다음, **완료**를 선택합니다.

    ![새 엔터티 속성이 설정된 팝업 대화 상자의 스크린샷](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. **의도**를 선택한 다음, **FindForm** 의도를 선택하여 발화에 레이블이 지정된 정규식을 확인합니다. 

    [![기존 엔터티 및 정규식 패턴이 사용된 발화 레이블 지정의 스크린샷](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    엔터티는 기계 학습 엔터티가 아니므로 레이블이 발화에 적용되고 만들어지는 즉시 LUIS 웹 사이트에 표시됩니다.

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `When were HRF-123456 and hrf-234567 published in the last year?`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발화 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 지정된 발화와 같지 않으므로 유용한 테스트이며, 두 양식 번호(`HRF-123456` 및 `hrf-234567`)가 있는 `FindForm` 의도가 반환되어야 합니다.

    ```
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    발화의 숫자는 두 번, 즉 한 번은 새로운 `hrf-number` 엔터티로, 그리고 한 번은 미리 작성된 `number` 엔터티로 반환됩니다. 발화에는 이 예제와 같이 둘 이상의 엔터티 및 둘 이상의 동일한 형식의 엔터티가 있을 수 있습니다. LUIS는 정규식 엔터티를 사용하여 명명된 데이터를 추출합니다. 이 데이터는 프로그래밍 방식으로 JSON 응답을 받는 클라이언트 응용 프로그램에 더 유용합니다.

## <a name="what-has-this-luis-app-accomplished"></a>이 LUIS 앱에서 수행한 작업은?
이 앱에서는 의도를 확인하고 추출한 데이터를 반환했습니다. 

챗봇에는 이제 기본 작업, `FindForm` 및 검색에 포함된 양식 번호를 결정하기에 충분한 정보가 있습니다. 

## <a name="where-is-this-luis-data-used"></a>이 LUIS 데이터가 사용되는 위치는? 
LUIS는 이 요청을 통해 수행됩니다. 챗봇과 같은 호출 응용 프로그램에서는 topScoringIntent 결과와 양식 번호를 사용하여 타사 API를 검색할 수 있습니다. LUIS는 이러한 작업을 수행하지 않습니다. LUIS는 사용자의 의도가 무엇인지 결정하고 해당 의도에 대한 데이터를 추출합니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [목록 엔터티에 대해 알아보기](luis-quickstart-intent-and-list-entity.md)

