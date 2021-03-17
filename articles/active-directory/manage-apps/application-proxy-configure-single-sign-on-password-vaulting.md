---
title: Jednotné přihlašování k aplikacím pomocí Azure Proxy aplikací služby AD | Microsoft Docs
description: Zapněte jednotné přihlašování pro vaše publikované místní aplikace s využitím Azure Proxy aplikací služby AD v Azure Portal.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253998"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Trezor hesel pro jednotné přihlašování s proxy aplikací

Proxy aplikací služby Azure Active Directory vám pomůže zlepšit produktivitu publikováním místních aplikací, aby k nim vzdálení zaměstnanci měli přístup. V Azure Portal můžete pro tyto aplikace také nastavit jednotné přihlašování (SSO). Uživatelé se potřebují jenom ověřit pomocí Azure AD a můžou získat přístup k podnikové aplikaci, aniž by se museli znovu přihlašovat.

Proxy aplikace podporuje několik [režimů jednotného přihlašování](sso-options.md#choosing-a-single-sign-on-method). Přihlašování založené na heslech je určeno pro aplikace, které používají kombinaci uživatelského jména a hesla pro ověřování. Když pro svou aplikaci nakonfigurujete přihlášení založené na heslech, uživatelé se musí přihlašovat do místní aplikace jenom jednou. Potom Azure Active Directory uloží přihlašovací údaje a automaticky ji poskytne aplikaci, když k ní uživatelé přistupují vzdáleně.

Svou aplikaci byste už měli publikovat a testovat pomocí proxy aplikace. Pokud ne, postupujte podle kroků v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md) pak se vraťte sem.

## <a name="set-up-password-vaulting-for-your-application"></a>Nastavení trezoru hesel pro vaši aplikaci

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
1. Vyberte **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou chcete nastavit s jednotným přihlašováním.  
1. Vyberte **proxy aplikace**. 
1. Změňte typ předběžného **ověřování** pro **průchozí** a vyberte **Uložit**. Později můžete znovu přejít zpět na **Azure Active Directory** typ. 
1. Vyberte **jednotné přihlašování**.

   ![Výběr jednotného přihlašování ze stránky přehled aplikace](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. V režimu jednotného přihlašování vyberte **přihlašování založené na heslech**.
1. Pro přihlašovací adresu URL zadejte adresu URL stránky, kde uživatelé zadají své uživatelské jméno a heslo pro přihlášení k aplikaci mimo podnikovou síť. Může to být externí adresa URL, kterou jste vytvořili při publikování aplikace prostřednictvím proxy aplikací.

   ![Vyberte přihlášení založené na heslech a zadejte adresu URL.](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Vyberte **Uložit**.
1. Vyberte **proxy aplikace**. 
1. **Typ předběžného ověření** změňte na **Azure Active Directory** a vyberte **Uložit**. 
1. Vyberte **Uživatelé a skupiny**.
1. Přiřaďte uživatele k aplikaci výběrem možnosti **Přidat uživatele**. 
1. Pokud chcete předem definovat přihlašovací údaje pro uživatele, zaškrtněte políčko před uživatelským jménem a vyberte **Aktualizovat přihlašovací údaje**.
1. Vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou jste nakonfigurovali pomocí jednotného přihlašování k heslům.
1. Vyberte **branding**. 
1. Aktualizujte **adresu URL domovské stránky** zadáním **adresy URL pro přihlášení** na stránce heslo SSO a vyberte **Uložit**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testování aplikace

Přejít na portál moje aplikace Přihlaste se pomocí svých přihlašovacích údajů (nebo přihlašovacích údajů pro testovací účet, který jste nastavili s přístupem). Po úspěšném přihlášení klikněte na ikonu aplikace. To může aktivovat instalaci zabezpečeného přihlašovacího prohlížeče pro moje aplikace. Pokud uživatel měl předdefinované přihlašovací údaje, mělo by se ověřování do aplikace provádět automaticky, jinak musíte zadat uživatelské jméno nebo heslo poprvé. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další způsoby implementace [jednotného přihlašování](what-is-single-sign-on.md) .
- Přečtěte si o [bezpečnostních faktorech při vzdáleném přístupu k aplikacím pomocí Azure proxy aplikací služby AD](application-proxy-security.md)
