---
title: Princip jednotného přihlašování pomocí aplikace proxy aplikací pomocí místní aplikace
description: Pochopení jednotného přihlašování pomocí aplikace proxy aplikací pomocí místní aplikace
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kenwith
ms.reviewer: japere, asteen
ms.openlocfilehash: 10b722edbe8d70c92e617c78db3d2fb1d46da3a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99254100"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Konfigurace jednotného přihlašování k aplikaci Proxy aplikací

Jednotné přihlašování (SSO) umožňuje uživatelům přístup k aplikaci bez ověřování několikrát. Umožňuje jednotné ověřování v cloudu, proti Azure Active Directory a umožňuje službě nebo konektoru zosobnit uživatele k dokončení jakýchkoli dalších výzev k ověření od aplikace.

## <a name="how-to-configure-single-sign-on"></a>Jak nakonfigurovat jednotné přihlašování
Pokud chcete nakonfigurovat jednotné přihlašování, nejdřív se ujistěte, že je aplikace nakonfigurovaná pro předběžné ověření prostřednictvím Azure Active Directory. Tuto konfiguraci provedete tak, že přejdete na **Azure Active Directory**  - &gt; **podnikové aplikace**  - &gt; **všechny aplikace**  - &gt; **- &gt; proxy** aplikace vaší aplikace. Na této stránce se zobrazí pole předběžného ověření a ujistěte se, že je nastavená možnost Azure Active Directory. 

Další informace o metodách předběžného ověření najdete v kroku 4 [dokumentu publikování aplikace](application-proxy-add-on-premises-application.md).

   ![Metoda předběžného ověřování v Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimů jednotného přihlašování pro aplikace proxy aplikací
Nakonfigurujte konkrétní typ jednotného přihlašování. Metody přihlašování jsou klasifikované na základě typu ověřování, které používá aplikace back-end. Aplikace proxy aplikací podporují tři typy přihlašování:

-   **Přihlašování založené na heslech**: přihlášení založené na heslech se dá použít pro aplikaci, která používá pole uživatelského jména a hesla k přihlášení. Postup konfigurace je v [konfiguraci jednotného přihlašování pro aplikaci Galerie Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrované ověřování systému Windows**: pro aplikace používající integrované ověřování systému Windows (IWA) je jednotné přihlašování povoleno prostřednictvím omezeného delegování protokolu Kerberos (KCD). Tato metoda poskytuje proxy konektorům aplikace oprávnění ve službě Active Directory k zosobnění uživatelů a posílání a přijímání tokenů jménem. Podrobnosti o konfiguraci KCD najdete v [dokumentaci k KCD v jednom Sign-On](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Přihlášení založené na hlavičkách**: přihlašování založené na hlavičkách slouží k poskytování možností jednotného přihlašování pomocí hlaviček protokolu HTTP. Další informace najdete v tématu [jednotné přihlašování založené na hlavičkách](application-proxy-configure-single-sign-on-with-headers.md).

-   **Jednotné přihlašování SAML**: s jednotným přihlašováním SAML se Azure AD ověřuje do aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Pomocí jednotného přihlašování založené na SAML můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Informace o nastavení jednotného přihlašování SAML najdete v tématu [SAML pro jednotné přihlašování pomocí proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Každou z těchto možností můžete najít tak, že v části podnikové aplikace zapnete aplikaci a v nabídce vlevo otevřete stránku **jednotného přihlašování** . Všimněte si, že pokud se vaše aplikace vytvořila na starém portálu, nemusí se tyto možnosti zobrazit.

Na této stránce se zobrazí také jedna další možnost Sign-On: propojené přihlašování. Tuto možnost podporuje i proxy aplikace. Tato možnost však nepřidá do aplikace jednotné přihlašování. To, že tato aplikace už může mít jednotné přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services (AD FS). 

Tato možnost umožňuje správci vytvořit odkaz na aplikaci, kterou uživatelé poprvé dojdou při přístupu k aplikaci. Pokud je například aplikace konfigurovaná pro ověřování uživatelů pomocí Active Directory Federation Services (AD FS) 2,0, může správce pomocí možnosti "propojené přihlašování" vytvořit odkaz na vlastní aplikace.

## <a name="next-steps"></a>Další kroky
- [Trezor hesel pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Omezené delegování protokolu Kerberos pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování pomocí hlaviček pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-headers.md) 
- [SAML pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md)
