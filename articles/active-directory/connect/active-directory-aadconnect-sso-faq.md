---
title: 'Azure AD Connect: Seamless Single Sign-On Single Sign-On - FAQ(질문과 대답) | Microsoft Docs'
description: Azure Active Directory Seamless Single Sign-On에 대한 질문과 대답입니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 29ed96044ceaa914db3f8b7090a1be5f65827e54
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627477"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory Seamless Single Sign-On: FAQ(질문과 대답)

이 문서에서는 Azure Active Directory Seamless SSO(Seamless Single Sign-On)에 대한 질문과 대답을 다룹니다. 새로운 내용을 계속 확인해 주세요.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Seamless SSO에서 사용되는 로그인 방법은 무엇인가요?

Seamless SSO는 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md) 또는 [통과 인증](active-directory-aadconnect-pass-through-authentication.md) 로그인 방법과 결합할 수 있습니다. 그러나 AD FS(Active Directory Federation Services)에는 이 기능을 사용할 수 없습니다.

## <a name="is-seamless-sso-a-free-feature"></a>Seamless SSO는 평가판 체험 기능인가요?

평가판 체험 기능이며, Azure AD 유료 버전으로 이 기능을 사용할 필요가 없습니다.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Seamless SSO는 [Microsoft Azure 독일 클라우드](http://www.microsoft.de/cloud-deutschland) 및 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서 사용할 수 있나요?

아니요. Seamless SSO는 Azure AD의 전 세계 인스턴스에서만 사용할 수 있습니다.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Seamless SSO의 `domain_hint` 또는 `login_hint` 매개 변수 기능을 활용하는 응용 프로그램은 무엇인가요?

다음은 이러한 매개 변수를 Azure AD에 전송할 수 있는 응용 프로그램의 부분 목록으로, Seamless SSO를 사용하여 자동 로그온 환경을 제공합니다(예: 사용자가 사용자 이름 또는 암호를 입력할 필요가 없음).

| 응용 프로그램 이름 | 사용할 응용 프로그램 URL |
| -- | -- |
| 액세스 패널 | https://myapps.microsoft.com/contoso.com |
| 웹용 Outlook | https://outlook.office365.com/contoso.com |
| Office 365 포털 | https://portal.office.com?domain_hint=contoso.com |

또한 응용 프로그램이 Azure AD의 공통 끝점(즉, https://login.microsoftonline.com/common/<...>) 대신 Azure AD의 테넌트 끝점(즉, https://login.microsoftonline.com/contoso.com/<..> 또는 https://login.microsoftonline.com/<tenant_ID>/<..>)으로 로그인 요청을 전송할 경우 사용자는 자동 로그온 환경을 사용합니다. 이러한 형식의 로그인을 요청하는 응용 프로그램의 부분 목록이 아래에 나열됩니다.

| 응용 프로그램 이름 | 사용할 응용 프로그램 URL |
| -- | -- |
| SharePoint Online | https://contoso.sharepoint.com |
| Azure portal | https://portal.azure.com/contoso.com |

위 표에 나오는 "contoso.com"을 도메인 이름으로 바꾸어 테넌트에 적절한 응용 프로그램 URL로 이동합니다.

다른 응용 프로그램에서 자동 로그온 환경을 사용하려는 경우 사용자 의견 섹션에서 알려주십시오.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Seamless SSO에서는 사용자 이름으로 `userPrincipalName` 대신 `Alternate ID`를 지원하는가요?

예. Seamless SSO는 [여기서](active-directory-aadconnect-get-started-custom.md) 보여주듯이 Azure AD Connect에서 구성할 때 `Alternate ID`를 사용자 이름으로 지원합니다. 모든 Office 365 응용 프로그램에서 `Alternate ID`를 지원하지는 않습니다. 지원 내용은 특정 응용 프로그램의 설명서를 참조하세요.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>[Azure AD 조인](../active-directory-azureadjoin-overview.md) 및 Seamless SSO에서 제공하는 Single Sign-On 환경 간에 차이점은 무엇인가요?

[Azure AD 조인](../active-directory-azureadjoin-overview.md)에서는 Azure AD에서 해당 장치를 등록한 경우 사용자에게 SSO를 제공합니다. 이러한 장치를 반드시 도메인에 가입할 필요는 없습니다. *기본 새로 고침 토큰*이나 *PRT* 및 Kerberos를 사용하여 SSO를 제공하지 않습니다. 사용자 환경은 Windows 10 장치에서 가장 적합합니다. SSO는 Edge 브라우저에서 자동으로 실행됩니다. 또한 브라우저 확장을 사용하여 Chrome에서 작동합니다.

테넌트에서 Azure AD 조인 및 Seamless SSO를 사용할 수 있습니다. 이러한 두 기능은 상호 보완적입니다. 두 기능이 모두 설정되어 있으면 Azure AD Join에서 SSO는 Seamless SSO보다 우선합니다.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>AD FS를 사용하지 않고 비Windows 10 장치를 Azure AD에 등록하려고 합니다. Seamless SSO를 대신 사용할 수 있나요?

예, 이 시나리오에는 [작업 공간 연결 클라이언트](https://www.microsoft.com/download/details.aspx?id=53554) 버전 2.1 이상이 필요합니다.

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>`AZUREADSSOACC` 컴퓨터 계정의 Kerberos 암호 해독 키를 롤오버하려면 어떻게 하나요?

온-프레미스 AD 포리스트에 만든 `AZUREADSSOACC` 컴퓨터 계정(Azure AD를 나타냄)의 Kerberos 암호 해독 키를 자주 롤오버하는 것이 중요합니다.

>[!IMPORTANT]
>적어도 30일마다 Kerberos 암호 해독 키를 롤오버하는 것이 좋습니다.

Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 따릅니다.

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>1단계. Seamless SSO가 사용하도록 설정된 AD 포리스트 목록을 가져옵니다.

1. 먼저 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드하여 설치합니다.
2. 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)을 다운로드하고 설치합니다.
3. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
4. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`
5. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령으로 테넌트의 전역 관리자 자격 증명을 입력하라는 팝업 메시지가 표시됩니다.
6. `Get-AzureADSSOStatus`를 호출합니다. 사용하도록 설정된 AD 포리스트 목록("도메인" 목록에 있음)이 표시됩니다.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>2단계. 설정된 각 AD 포리스트에서 Kerberos 암호 해독 키를 업데이트합니다.

1. `$creds = Get-Credential`를 호출합니다. 메시지가 표시되면 의도한 AD 포리스트에 대한 도메인 관리자 자격 증명을 입력합니다.

    >[!NOTE]
    >의도된 AD 포리스트를 찾기 위해 도메인 관리자의 사용자 이름을 사용합니다. 해당 항목은 UPN(사용자 계정 이름)(johndoe@contoso.com) 형식이나 도메인 정규화 SAM 계정 이름(contoso\johndoe 또는 contoso.com\johndoe) 형식으로 제공됩니다. 도메인 정규화 SAM 계정 이름을 사용하는 경우 사용자 이름의 도메인 부분을 사용하여 [DNS를 사용하는 도메인 관리자의 도메인 컨트롤러를 찾습니다](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). 대신 UPN을 사용하는 경우 적절한 도메인 컨트롤러를 찾기 전에 [도메인 정규화 SAM 계정 이름으로 변환](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)합니다.

2. `Update-AzureADSSOForest -OnPremCredentials $creds`를 호출합니다. 이 명령은 이 특정 AD 포리스트에서 `AZUREADSSOACC` 컴퓨터 계정에 대한 Kerberos 암호 해독 키를 업데이트하고 Azure AD에서 키를 업데이트 합니다.
3. 기능을 설정한 각 AD 포리스트에 대해 위의 단계를 반복합니다.

>[!IMPORTANT]
>`Update-AzureADSSOForest` 명령을 두 번 이상 실행하지 _않아야 합니다_. 그렇지 않으면 해당 기능은 사용자의 Kerberos 티켓이 만료되고 온-프레미스 Active Directory에 의해 재발급될 때까지 작동하지 않습니다.

## <a name="how-can-i-disable-seamless-sso"></a>Seamless SSO를 사용하지 않으려면 어떻게 해야 하나요?

### <a name="step-1-disable-the-feature-on-your-tenant"></a>1단계. 테넌트에서 기능 사용 안 함

#### <a name="option-a-disable-using-azure-ad-connect"></a>옵션 A: Azure AD Connect를 사용하여 사용하지 않도록 설정

1. Azure AD Connect를 실행하고 **사용자 로그인 페이지 변경**을 선택하고 **다음**을 클릭합니다.
2. **Single Sign-On 사용** 옵션의 선택을 취소합니다. 마법사를 계속 진행합니다.

마법사를 완료하면 테넌트에서 Seamless SSO를 사용하지 않도록 설정됩니다. 그러나 다음과 같은 메시지가 화면에 표시됩니다.

“Single Sign-On을 이제 사용하지 않도록 설정했습니다. 하지만 정리를 완료하기 위해 수행할 추가 수동 단계가 남아 있습니다. 자세한 정보”

정리 과정을 완료하려면 Azure AD Connect를 실행 중인 온-프레미스 서버에서 2단계 및 3단계를 따릅니다.

#### <a name="option-b-disable-using-powershell"></a>옵션 B: PowerShell을 사용하여 사용하지 않도록 설정

Azure AD Connect를 실행 중인 온-프레미스 서버에서 다음 단계를 실행합니다.

1. 먼저 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드하여 설치합니다.
2. 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치합니다.
3. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
4. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`
5. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령으로 테넌트의 전역 관리자 자격 증명을 입력하라는 팝업 메시지가 표시됩니다.
6. `Enable-AzureADSSO -Enable $false`를 호출합니다.

>[!IMPORTANT]
>PowerShell을 사용하여 Seamless SSO를 사용하지 않도록 설정하면 Azure AD Connect의 상태를 변경하지 않습니다. Seamless SSO는 **사용자 로그인 변경** 페이지에서 사용하도록 설정된 것으로 표시됩니다.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>2단계. Seamless SSO가 사용하도록 설정된 AD 포리스트 목록을 가져옵니다.

Azure AD Connect를 사용하여 Seamless SSO를 사용하지 않도록 설정한 경우 아래 1-5단계를 따릅니다. 대신 PowerShell을 사용하여 Seamless SSO를 사용하지 않도록 설정한 경우 아래의 6단계로 이동합니다.

1. 먼저 [Microsoft Online Services 로그인 도우미](http://go.microsoft.com/fwlink/?LinkID=286152)를 다운로드하여 설치합니다.
2. 그런 다음 [Windows PowerShell 용 64비트 Azure Active Directory 모듈](http://go.microsoft.com/fwlink/p/?linkid=236297)을 다운로드하고 설치합니다.
3. `%programfiles%\Microsoft Azure Active Directory Connect` 폴더로 이동합니다.
4. 다음 명령을 사용하여 Seamless SSO PowerShell 모듈을 가져옵니다. `Import-Module .\AzureADSSO.psd1`
5. 관리자 권한으로 PowerShell을 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출합니다. 이 명령으로 테넌트의 전역 관리자 자격 증명을 입력하라는 팝업 메시지가 표시됩니다.
6. `Get-AzureADSSOStatus`를 호출합니다. 사용하도록 설정된 AD 포리스트 목록("도메인" 목록에 있음)이 표시됩니다.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>3단계. 나열된 각 AD 포리스트에서 `AZUREADSSOACCT` 컴퓨터 계정을 수동으로 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [**빠른 시작**](active-directory-aadconnect-sso-quick-start.md) - Azure AD Seamless SSO를 준비하고 실행합니다.
- [**기술 심층 분석**](active-directory-aadconnect-sso-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
