---
title: Jednotné přihlašování do aplikací pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Zapnutí jedné přihlašování pro aplikace publikované v místním s Proxy aplikací Azure AD na webu Azure Portal.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025737"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Heslo vaulting pro jednotné přihlašování s Proxy aplikací

Azure Active Directory Application Proxy vám pomůže zlepšit produktivitu a publikujte místní aplikace tak, aby vzdálení zaměstnanci můžou bezpečně k nim přístup, příliš. Na webu Azure Portal můžete také nastavíte jednotné přihlašování (SSO) k těmto aplikacím. Uživatelé potřebují jenom k ověření ve službě Azure AD a bude moct své podnikové aplikace bez nutnosti znovu přihlásit.

Proxy aplikací podporuje několik [jednotného přihlašování režimy](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Přihlašování na základě heslo je určená pro aplikace, které používají kombinace uživatelského jména a hesla pro ověřování. Když konfigurujete přihlašování na základě heslo pro vaši aplikaci, pro přihlášení k místní aplikaci jednou mít uživatelé. Azure Active Directory, ukládá přihlašovací údaje a automaticky poskytuje k aplikaci při vaši uživatelé k němu přistupovat vzdáleně.

By měl už máte publikování a testování aplikace s využitím Proxy aplikací. Pokud ne, postupujte podle kroků v [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md) poté vraťte se sem.

## <a name="set-up-password-vaulting-for-your-application"></a>Nastavit heslo vaulting pro vaši aplikaci

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
1. Vyberte **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou chcete nastavit jednotné přihlašování.  
1. Vyberte **Proxy aplikací**. 
1. Změňte typ předběžného **ověřování** pro **průchozí** a vyberte **Uložit**. Později můžete znovu přejít zpět na **Azure Active Directory** typ. 
1. Vyberte **jednotného přihlašování**.

   ![Výběr jednotného přihlašování ze stránky přehled aplikace](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. Režim jednotného přihlašování, zvolte **přihlašování na základě heslo**.
1. Přihlašovací adresy URL zadejte adresu URL pro stránku, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení k aplikaci mimo podnikovou síť. To může být externí adresu URL, kterou jste vytvořili při publikování aplikace prostřednictvím Proxy aplikací.

   ![Zvolte přihlašování na základě hesla a zadejte adresu URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. Vyberte **Uložit**.
1. Vyberte **Proxy aplikací**. 
1. **Typ předběžného ověření** změňte na **Azure Active Directory** a vyberte **Uložit**. 
1. Vyberte **Uživatelé a skupiny**.
1. Přiřaďte uživatele k aplikaci výběrem možnosti **Přidat uživatele**. 
1. Pokud chcete předem definovat přihlašovací údaje pro uživatele, zaškrtněte políčko před uživatelským jménem a vyberte **Aktualizovat přihlašovací údaje**.
1. Vyberte **Azure Active Directory** > **Registrace aplikací** > **všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou jste nakonfigurovali pomocí jednotného přihlašování k heslům.
1. Vyberte **branding**. 
1. Aktualizujte **adresu URL domovské stránky** zadáním **adresy URL pro přihlášení** na stránce heslo SSO a vyberte **Uložit**.  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testování aplikace

Přejít na portál moje aplikace Přihlaste se pomocí svých přihlašovacích údajů (nebo přihlašovacích údajů pro testovací účet, který jste nastavili s přístupem). Po úspěšném přihlášení klikněte na ikonu aplikace. To může aktivovat instalaci zabezpečeného přihlašovacího prohlížeče pro moje aplikace. Pokud uživatel měl předdefinované přihlašovací údaje, mělo by se ověřování do aplikace provádět automaticky, jinak musíte zadat uživatelské jméno nebo heslo poprvé. 

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o další možnosti, jak implementovat [jednotného přihlašování](what-is-single-sign-on.md)
- Další informace o [aspekty zabezpečení pro přístup k aplikacím s využitím Azure AD Application Proxy](application-proxy-security.md)
