---
title: Azure AD 앱에 그룹 할당 | Microsoft Docs'
description: Azure 응용 프로그램에 대해 그룹 할당을 구현하는 방법.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: 471099c58a7fc1dfd520e693873a76f64c0fbf0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
ms.locfileid: "26597146"
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>응용 프로그램에 Azure Active Directory 그룹 할당
응용 프로그램에 사용자 및 그룹을 할당하기 전에 사용자 할당을 요구해야 합니다. 사용자 할당을 요구하는 방법에 대한 내용은 [사용자 할당 요구](active-directory-applications-guiding-developers-requiring-user-assignment.md) 문서를 참조하세요.

이 문서에서는 이 응용 프로그램에 대해 사용하는 Active Directory에서 그룹을 이미 만들었다고 가정합니다.

## <a name="assigning-groups-to-an-application"></a>응용 프로그램에 그룹 지정
1. 관리자 계정으로 Azure 포털에 로그인합니다.
2. 주 메뉴에서 **모든 항목** 항목을 클릭합니다.
3. 응용 프로그램에 대해 사용하는 디렉터리를 선택합니다.
4. **응용 프로그램** 탭을 클릭합니다.
5. 이 디렉터리와 연결된 응용 프로그램 목록에서 응용 프로그램을 선택합니다.
6. **사용자 및 그룹** 탭을 클릭합니다.
7. **그룹** 드롭다운 목록을 사용하여 Active Directory에서 그룹 목록을 필터링합니다.
8. 그룹을 선택합니다.
9. **할당**을 클릭합니다.
10. 메시지가 표시되면 **예** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
