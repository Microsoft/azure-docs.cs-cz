---
title: Jednotné přihlašování k aplikacím pomocí proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Zapněte jednotné přihlašování pro publikované místní aplikace pomocí proxy aplikací Azure AD na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0259a8d9fcb4c9c513ab2c31103c9a8488e90ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025737"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Trezor hesel pro jednotné přihlašování pomocí proxy aplikace

Azure Active Directory Application Proxy vám pomůže zvýšit produktivitu publikováním místních aplikací, aby k nim měli bezpečně přístup i vzdálení zaměstnanci. Na webu Azure Portal můžete také nastavit jednotné přihlašování (SSO) k těmto aplikacím. Vaši uživatelé potřebují k ověření pouze pomocí Služby Azure AD a mají přístup k podnikové aplikaci, aniž by se museli znovu přihlašovat.

Proxy aplikace podporuje několik [režimů jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Přihlášení na základě hesla je určeno pro aplikace, které používají kombinaci uživatelského jména a hesla pro ověřování. Při konfiguraci přihlášení na základě hesla pro vaši aplikaci, vaši uživatelé se musí přihlásit k místní aplikaci jednou. Poté služba Azure Active Directory uloží přihlašovací informace a automaticky je poskytne aplikaci, když k nim uživatelé vzdáleně přistupují.

Měli jste již publikovat a testovat aplikaci s proxy aplikací. Pokud ne, postupujte podle kroků v [publikovat aplikace pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md) a pak se sem vraťte.

## <a name="set-up-password-vaulting-for-your-application"></a>Nastavení trezoru hesel pro vaši aplikaci

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
1. Vyberte**aplikace** >  **Azure Active Directory** > Enterprise**Všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou chcete nastavit pomocí přiřazuje k řazeným.  
1. Vyberte **proxy aplikace**. 
1. Změňte **typ předběžného ověřování** na **passthrough** a vyberte **Uložit**. Později můžete přepnout zpět na typ **služby Azure Active Directory** znovu! 
1. Vyberte **možnost Jednotné přihlašování**.

   ![Výběr jednotného přihlášení na stránce s přehledem aplikace](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Pro režim s přihlašování zvolte **přihlášení na základě hesla**.
1. Pro přihlašovací adresu URL zadejte adresu URL stránky, na které uživatelé zadají své uživatelské jméno a heslo pro přihlášení k vaší aplikaci mimo podnikovou síť. Může se jedná o externí adresu URL, kterou jste vytvořili při publikování aplikace prostřednictvím proxy aplikace.

   ![Zvolte přihlášení založené na heslech a zadejte adresu URL.](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Vyberte **Uložit**.
1. Vyberte **proxy aplikace**. 
1. Změňte **typ předběžného ověřování** na **Službu Azure Active Directory** a vyberte **Uložit**. 
1. Vyberte **možnost Uživatelé a skupiny**.
1. Přiřaďte uživatelům aplikaci možnost **Přidat uživatele**. 
1. Pokud chcete předdefinovat pověření pro uživatele, zaškrtněte políčko na přední straně uživatelského jména a vyberte **Aktualizovat pověření**.
1. Vyberte**registrace** > aplikací **Azure Active Directory** > **Všechny aplikace**.
1. Ze seznamu vyberte aplikaci, kterou jste nakonfigurovali pomocí semanového zabezpečení hesla.
1. Vyberte **branding**. 
1. Aktualizujte **adresu URL domovské stránky** pomocí adresy URL přihlášení **na** stránce SSO hesla a vyberte **Uložit**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testování aplikace

Přejděte na portál Moje aplikace. Přihlaste se pomocí přihlašovacích údajů (nebo přihlašovacích údajů pro testovací účet, který jste nastavili s přístupem). Po úspěšném přihlášení klikněte na ikonu aplikace. To může spustit instalaci rozšíření prohlížeče Zabezpečené přihlášení moje aplikace. Pokud měl uživatel předdefinovaná pověření, mělo by k ověření aplikace dojít automaticky, jinak musíte poprvé zadat uživatelské jméno nebo heslo. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si o dalších způsobech implementace [jednotného přihlašování](what-is-single-sign-on.md)
- Informace o [aspektech zabezpečení pro vzdálený přístup k aplikacím pomocí proxy aplikací Azure AD](application-proxy-security.md)
