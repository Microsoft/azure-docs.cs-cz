---
title: Windows Virtual Desktop에서 테넌트 만들기 - Azure
description: Azure Active Directory에서 Windows Virtual Desktop 테넌트를 설정하는 방법을 설명합니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: helohr
ms.openlocfilehash: 04a65442e86168239f08fb71303b8d9e9e152e72
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "71679473"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>자습서: Windows 가상 데스크톱에서 테 넌 트 만들기

Windows Virtual Desktop에서 테넌트를 만드는 것은 데스크톱 가상화 솔루션을 빌드하기 위한 첫 번째 단계입니다. 테넌트는 하나 이상의 호스트 풀로 구성된 그룹입니다. 각 호스트 풀은 Azure에서 가상 머신으로 실행되고 Windows Virtual Desktop 서비스에 등록된 여러 세션 호스트로 구성됩니다. 또한 각 호스트 풀은 사용자에게 원격 데스크톱 및 원격 애플리케이션 리소스를 게시하는 데 사용되는 하나 이상의 앱 그룹으로 구성됩니다. 테넌트를 사용하여 호스트 풀을 빌드하고, 앱 그룹을 만들고, 사용자를 할당하고, 서비스를 통한 연결을 설정할 수 있습니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Windows Virtual Desktop 서비스에 Azure Active Directory 권한 부여
> * Azure Active Directory 테넌트의 사용자에게 TenantCreator 애플리케이션 역할 할당
> * Windows Virtual Desktop 테넌트 만들기

Windows Virtual Desktop 테넌트를 설정하는 데 필요한 것은 다음과 같습니다.

* Windows Virtual Desktop 사용자의 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 테넌트 ID
* Azure Active Directory 테넌트 내 글로벌 관리자 계정
   * 이는 또한 고객의 Windows Virtual Desktop 테넌트를 만드는 CSP(클라우드 솔루션 공급자) 조직에도 적용됩니다. CSP 조직에 속한 경우 고객 Azure Active Directory 인스턴스의 글로벌 관리자로 로그인할 수 있어야 합니다.
   * Windows Virtual Desktop 테넌트를 만들려는 경우 Azure Active Directory 테넌트에서 관리자 계정을 가져와야 합니다. 이 프로세스는 Azure Active Directory B2B(게스트) 계정을 지원하지 않습니다.
   * 관리자 계정은 회사 또는 학교 계정이어야 합니다.
* Azure 구독.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Windows Virtual Desktop에 사용 권한 부여

Windows Virtual Desktop에 이 Azure Active Directory 인스턴스에 대한 권한을 이미 부여한 경우에는 이 섹션을 건너뛰세요.

Windows Virtual Desktop 서비스에 권한을 부여하면 이 서비스에서 Azure Active Directory에 관리자 및 최종 사용자 작업을 쿼리할 수 있습니다.

서비스 권한을 부여하려면:

1. 브라우저를 열고 [Windows Virtual Desktop 서버 앱](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)에 대한 관리자 동의 흐름을 시작합니다.
   > [!NOTE]
   > 고객을 관리하고 고객의 디렉터리에 대한 관리자 동의를 부여해야 하는 경우 브라우저에 다음 URL을 입력하고 {tenant}를 고객의 Azure AD 도메인 이름으로 바꿉니다. 예를 들어 고객의 조직에서 contoso.onmicrosoft.com의 Azure AD 도메인 이름을 등록한 경우 {tenant}를 contoso.onmicrosoft.com으로 바꿉니다.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. 글로벌 관리자 계정을 사용하여 Windows Virtual Desktop 동의 페이지에 로그인합니다. 예를 들어 Contoso 조직의 사용자 계정은 admin@contoso.com 또는 admin@contoso.onmicrosoft.com일 수 있습니다.
3. **수락**을 선택합니다.
4. Azure AD가 동의를 기록할 수 있도록 1분 동안 기다립니다.
5. 브라우저를 열고 [Windows Virtual Desktop 클라이언트 앱](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)에 대한 관리자 동의 흐름을 시작합니다.
   >[!NOTE]
   > 고객을 관리하고 고객의 디렉터리에 대한 관리자 동의를 부여해야 하는 경우 브라우저에 다음 URL을 입력하고 {tenant}를 고객의 Azure AD 도메인 이름으로 바꿉니다. 예를 들어 고객의 조직에서 contoso.onmicrosoft.com의 Azure AD 도메인 이름을 등록한 경우 {tenant}를 contoso.onmicrosoft.com으로 바꿉니다.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 2단계와 마찬가지로 글로벌 관리자로 Windows Virtual Desktop 동의 페이지에 로그인합니다.
7. **수락**을 선택합니다.

## <a name="assign-the-tenantcreator-application-role"></a>TenantCreator 애플리케이션 역할 할당

Azure Active Directory 사용자에게 TenantCreator 애플리케이션 역할을 할당하면 사용자가 Azure Active Directory 인스턴스와 연결된 Windows Virtual Desktop 테넌트를 만들 수 있습니다. TenantCreator 역할을 할당하려면 글로벌 관리자 계정을 사용해야 합니다.

TenantCreator 애플리케이션 역할을 할당하려면 다음을 수행합니다.

1. 브라우저를 열고 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 연결합니다.
   
   여러 Azure Active Directory 테넌트로 작업하는 경우 프라이빗 브라우저 세션을 열고 URL을 복사한 후 주소 창에 붙여넣는 것이 가장 좋습니다.
2. Azure Portal 내의 검색 표시줄에서 **엔터프라이즈 애플리케이션**을 검색하고 **서비스** 범주 아래에 나타나는 항목을 선택합니다.
3. **엔터프라이즈 애플리케이션** 내에서 **Windows Virtual Desktop**을 검색합니다. 이전 섹션에서 동의를 제공한 두 애플리케이션이 표시됩니다. 이러한 두 앱 중 **Windows Virtual Desktop**을 선택합니다.
   !["엔터프라이즈 애플리케이션"에서 "Windows Virtual Desktop"을 검색하는 경우의 검색 결과 스크린샷입니다. "Windows Virtual Desktop"이라는 앱이 강조 표시됩니다.](media/tenant-enterprise-app.png)
4. **사용자 및 그룹**을 선택합니다. 애플리케이션에 대한 동의를 부여한 관리자가 **기본 액세스** 역할이 할당된 상태로 목록에 표시되는 것을 볼 수 있습니다. 이것만으로는 Windows Virtual Desktop 테넌트를 만드는 데 충분하지 않습니다. 사용자에게 **TenantCreator** 역할을 추가하려면 다음 지침에 따라 계속 진행합니다.
   !["Windows Virtual Desktop" 엔터프라이즈 애플리케이션을 관리하도록 할당된 사용자 및 그룹 스크린샷입니다. 이 스크린샷은 "기본 액세스"에 대한 할당을 하나만 표시합니다.](media/tenant-default-access.png)
5. **사용자 추가**를 선택한 다음, **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택합니다.
6. Windows Virtual Desktop 테넌트를 만들 사용자 계정을 검색합니다. 편의상 이는 글로벌 관리자 계정일 수 있습니다.
   - contosoadmin@live.com 또는 contosoadmin@outlook.com과 같은 Microsoft ID 공급자를 사용하는 경우 Windows Virtual Desktop에 로그인하지 못할 수도 있습니다. admin@contoso.com 또는 admin@contoso.onmicrosoft.com과 같은 도메인 특정 계정을 사용하는 것이 좋습니다.

   !["TenantCreator"로 추가할 사용자를 선택하는 스크린샷입니다.](media/tenant-assign-user.png)

   > [!NOTE]
   > 이 Azure Active Directory 인스턴스에서 소싱되는 사용자(또는 사용자를 포함하는 그룹)를 선택해야 합니다. 게스트(B2B) 사용자 또는 서비스 주체는 선택할 수 없습니다.

7. 사용자 계정을 선택하고 **선택** 단추를 선택한 후 **할당**을 선택합니다.
8. **Windows Virtual Desktop - 사용자 및 그룹** 페이지에서 Windows Virtual Desktop 테넌트를 만들 사용자에게 **TenantCreator** 역할이 할당된 새 항목이 표시되는지 확인합니다.
   !["Windows Virtual Desktop" 엔터프라이즈 애플리케이션을 관리하도록 할당된 사용자 및 그룹 스크린샷입니다. 이 스크린샷은 이제 "TenantCreator" 역할에 할당된 사용자의 두 번째 항목을 포함합니다.](media/tenant-tenant-creator-added.png)

계속해서 Windows Virtual Desktop 테넌트를 만들려면 두 가지 정보가 필요합니다.
- Azure Active Directory의 테넌트 ID(또는 **디렉터리 ID**)
- Azure 구독 ID

Azure Active Directory 테넌트 ID(또는 **디렉터리 ID**)를 찾으려면 다음을 수행합니다.
1. 동일한 Azure Portal 세션의 검색 표시줄에서 **Azure Active Directory**를 검색하고 **서비스** 범주 아래에 나타나는 항목을 선택합니다.
   ![Azure Portal에서 "Azure Active Directory"를 검색한 결과의 스크린샷입니다. “서비스” 아래의 검색 결과가 강조 표시됩니다.](media/tenant-search-azure-active-directory.png)
2. **속성**을 찾을 때까지 아래로 스크롤한 후 선택합니다.
3. **디렉터리 ID**를 찾은 후 클립보드 아이콘을 선택합니다. 나중에 **AadTenantId** 값으로 사용할 수 있게 편리한 위치에 붙여넣습니다.
   ![Azure Active Directory 속성이 스크린샷입니다. 복사하여 붙여넣을 "디렉터리 ID"의 클립보드 아이콘 위로 마우스를 가져가고 있습니다.](media/tenant-directory-id.png)

Azure 구독 ID를 찾으려면
1. 동일한 Azure Portal 세션의 검색 표시줄에서 **구독**을 검색하고 **서비스** 범주 아래에 나타나는 항목을 선택합니다.
   ![Azure Portal에서 "Azure Active Directory"를 검색한 결과의 스크린샷입니다. “서비스” 아래의 검색 결과가 강조 표시됩니다.](media/tenant-search-subscription.png)
2. Windows Virtual Desktop 서비스 알림을 수신하는 데 사용할 Azure 구독을 선택합니다.
3. **구독 ID**를 검색한 후 클립보드 아이콘이 나타날 때까지 값 위로 마우스를 가져갑니다. 클립보드 아이콘을 선택하고 나중에 **AzureSubscriptionId** 값으로 사용할 수 있게 편리한 위치에 붙여넣습니다.
   ![Azure 구독 속성의 스크린샷입니다. 복사하여 붙여넣을 "구독 ID"의 클립보드 아이콘 위로 마우스를 가져가고 있습니다.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows Virtual Desktop 테넌트 만들기

Windows Virtual Desktop 서비스에 Azure Active Directory 쿼리 권한을 부여했고, 사용자 계정에 TenantCreator 역할을 부여했으면, Windows Virtual Desktop 테넌트를 만들 수 있습니다.

먼저 PowerShell 세션에서 사용할 [Windows Virtual Deskto 모듈을 다운로드하고 가져옵니다](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)(아직 다운로드하고 가져오지 않은 경우).

이 cmdlet에 TenantCreator 사용자 계정을 사용하여 Windows Virtual Desktop에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

그 후 Azure Active Directory 테넌트와 연결된 새로운 Windows Virtual Desktop 테넌트를 만듭니다.

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

대괄호로 묶인 값은 조직 및 테넌트와 관련된 값으로 바꿉니다. 새 Windows Virtual Desktop 테넌트에 대해 선택한 이름은 전역적으로 고유해야 합니다. 예를 들어 Contoso 조직의 Windows Virtual Desktop TenantCreator라고 가정해 보겠습니다. 이 경우 다음과 같은 cmdlet을 실행할 수 있습니다.

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>다음 단계

테넌트를 만든 후 Azure Active Directory에서 서비스 주체를 만들고 Windows Virtual Desktop 내에서 역할을 할당해야 합니다. 이 서비스 주체를 사용하면 Windows Virtual Desktop Azure Marketplace 제품을 배포하여 호스트 풀을 만들 수 있습니다. 호스트 풀에 대해 자세히 알아보려면 Windows Virtual Desktop에서 호스트 풀을 만드는 방법에 대한 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [PowerShell을 사용하여 서비스 주체 만들기 및 역할 할당](./create-service-principal-role-powershell.md)
