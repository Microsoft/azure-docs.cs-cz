---
title: Publikování v = místní SharePoint s aplikačním proxy serverem – Azure AD
description: Obsahuje základní informace o tom, jak integrovat místní SharePointový Server s Azure Proxy aplikací služby AD pro SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34aaafcd03e737b1e59529f8001e0c008bd39b70
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888868"
---
# <a name="integrate-with-sharepoint-saml"></a>Integrace se službou SharePoint (SAML)

Tento podrobný průvodce vysvětluje, jak zabezpečit přístup k [Azure Active Directory integrovaným v místním SharePointu (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) pomocí Azure proxy aplikací služby AD, kde se uživatelé ve vaší organizaci (Azure AD, B2B) připojují k SharePointu přes Internet.

> [!NOTE] 
> Pokud s Azure začínáte Proxy aplikací služby AD a chcete získat další informace, přečtěte si téma [vzdálený přístup k místním aplikacím prostřednictvím Azure proxy aplikací služby AD](./application-proxy.md).

Toto nastavení má tři hlavní výhody:

- Azure Proxy aplikací služby AD zajišťuje, aby ověřený přenos mohl dosáhnout vaší interní sítě a serveru SharePoint.
- Uživatelé mají přístup k sharepointovým webům obvyklým bez použití sítě VPN.
- Přístup můžete řídit přiřazováním uživatelů na úrovni Azure Proxy aplikací služby AD a můžete zvýšit zabezpečení pomocí funkcí služby Azure AD, jako je podmíněný přístup a Multi-Factor Authentication (MFA).

Tento proces vyžaduje dvě podnikové aplikace. Jedna je místní instance SharePointu, kterou publikujete z Galerie do svého seznamu spravovaných aplikací SaaS. Druhým je místní aplikace (aplikace mimo galerii), kterou použijete k publikování první aplikace podnikové galerie.

## <a name="prerequisites"></a>Předpoklady

K dokončení této konfigurace potřebujete tyto prostředky:
 - Farma SharePoint 2013 nebo novější. Sharepointová farma musí být [integrovaná se službou Azure AD](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Tenant Azure AD s plánem, který zahrnuje proxy aplikace. Přečtěte si další informace o [plánech a cenách Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
 - [Vlastní ověřená doména](../fundamentals/add-custom-domain.md) v TENANTOVI Azure AD. Ověřená doména se musí shodovat s příponou adresy URL SharePointu.
 - Certifikát SSL je povinný. Podívejte se na podrobnosti v tématu [publikování vlastních domén](./application-proxy-configure-custom-domain.md).
 - Místní uživatelé služby Active Directory musí být synchronizováni s Azure AD Connect a musí být nakonfigurováni pro [přihlášení k Azure](../hybrid/plan-connect-user-signin.md). 
 - Pro uživatele hostované jenom pro Cloud a B2B je potřeba [udělit přístup k účtu Guest k místnímu SharePointu v Azure Portal](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Konektor proxy aplikací nainstalovaný a spuštěný v počítači v podnikové doméně.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Krok 1: integrace služby SharePoint v místním prostředí se službou Azure AD 

1. Nakonfigurujte místní aplikaci SharePoint. Další informace najdete v tématu [kurz: Azure Active Directory integraci jednotného přihlašování s místním SharePointem](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Před přechodem k dalšímu kroku Ověřte konfiguraci. K ověření se pokuste získat přístup k místnímu SharePointu z interní sítě a potvrdit, že je interní přístupný. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Krok 2: publikování místní aplikace SharePoint s proxy aplikací

V tomto kroku vytvoříte v tenantovi Azure AD aplikaci, která používá proxy aplikace. Nastavíte externí adresu URL a zadáte interní adresu URL, kterou použijete později v SharePointu.

> [!NOTE] 
> Interní a externí adresy URL se musí shodovat s **adresou URL pro přihlášení** v konfiguraci aplikace založené na SAML v kroku 1.

   ![Snímek obrazovky zobrazující hodnotu adresy URL pro přihlášení](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Vytvořte novou aplikaci Azure Proxy aplikací služby AD s vlastní doménou. Podrobné pokyny najdete v tématu [vlastní domény v Azure proxy aplikací služby AD](./application-proxy-configure-custom-domain.md).

    - Interní adresa URL: ' https://portal.contoso.com/ '
    - Externí adresa URL: ' https://portal.contoso.com/ '
    - Předběžné ověření: Azure Active Directory
    - Přeložit adresy URL v hlavičkách: ne
    - Přeložit adresy URL v těle aplikace: ne

        ![Snímek obrazovky zobrazující možnosti, které můžete použít k vytvoření aplikace](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Přiřaďte [stejné skupiny](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) , které jste přiřadili do místní aplikace sharepointové galerie.

3. Nakonec přejít do oddílu **vlastnosti** a nastavit jako **viditelné pro uživatele?** na **ne**. Tato možnost zajistí, že se na portálu moje aplikace zobrazí jenom ikona první aplikace ( https://myapplications.microsoft.com) .

   ![Snímek obrazovky s informacemi o tom, kde nastavit viditelné pro uživatele? nastavení.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Krok 3: testování aplikace

Pomocí prohlížeče z počítače v externí síti přejděte na odkaz, který jste nakonfigurovali během kroku publikování. Ujistěte se, že se můžete přihlásit pomocí zkušebního účtu, který jste nastavili.