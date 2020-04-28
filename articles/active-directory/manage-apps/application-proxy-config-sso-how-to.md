---
title: Jak nakonfigurovat jednotné přihlašování k aplikaci proxy aplikace
description: Jak rychle nakonfigurovat jednotné přihlašování k aplikaci proxy aplikací
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76712012"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Konfigurace jednotného přihlašování k aplikaci proxy aplikací

Jednotné přihlašování (SSO) umožňuje uživatelům přístup k aplikaci bez ověřování několikrát. Umožňuje jednotné ověřování v cloudu, proti Azure Active Directory a umožňuje službě nebo konektoru zosobnit uživatele k dokončení jakýchkoli dalších výzev k ověření od aplikace.

## <a name="how-to-configure-single-sign-on"></a>Jak nakonfigurovat jednotné přihlašování
Pokud chcete nakonfigurovat jednotné přihlašování, nejdřív se ujistěte, že je aplikace nakonfigurovaná pro předběžné ověření prostřednictvím Azure Active Directory. Tuto konfiguraci provedete tak, že přejdete na **Azure Active Directory**  - &gt; **podnikové aplikace**  - &gt; **všechny aplikace**  - &gt; ** - &gt; proxy**aplikace vaší aplikace. Na této stránce se zobrazí pole předběžného ověření a ujistěte se, že je nastavená možnost Azure Active Directory. 

Další informace o metodách předběžného ověření najdete v kroku 4 [dokumentu publikování aplikace](application-proxy-add-on-premises-application.md).

   ![Metoda předběžného ověřování v Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimů jednotného přihlašování pro aplikace proxy aplikací
Nakonfigurujte konkrétní typ jednotného přihlašování. Metody přihlašování jsou klasifikované na základě typu ověřování, které používá aplikace back-end. Aplikace proxy aplikací podporují tři typy přihlašování:

-   **Přihlašování založené na heslech**: přihlášení založené na heslech se dá použít pro aplikaci, která používá pole uživatelského jména a hesla k přihlášení. Postup konfigurace je v [konfiguraci jednotného přihlašování pro aplikaci Galerie Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrované ověřování systému Windows**: pro aplikace používající integrované ověřování systému Windows (IWA) je jednotné přihlašování povoleno prostřednictvím omezeného delegování protokolu Kerberos (KCD). Tato metoda poskytuje proxy konektorům aplikace oprávnění ve službě Active Directory k zosobnění uživatelů a posílání a přijímání tokenů jménem. Podrobnosti o konfiguraci KCD najdete v [dokumentaci k KCD v jednotném přihlašování](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Přihlašování založené na hlavičkách**: přihlašování založené na hlavičkách je povolené prostřednictvím partnerství a vyžaduje další konfiguraci. Podrobnosti o partnerství a podrobné pokyny pro konfiguraci jednotného přihlašování k aplikaci, která používá hlavičky pro ověřování, najdete v [dokumentaci k PingAccess pro Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Jednotné přihlašování SAML**: s jednotným přihlašováním SAML se Azure AD ověřuje do aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Pomocí jednotného přihlašování založené na SAML můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Informace o nastavení jednotného přihlašování SAML najdete v tématu [SAML pro jednotné přihlašování pomocí proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Každou z těchto možností můžete najít tak, že v části podnikové aplikace zapnete aplikaci a v nabídce vlevo otevřete stránku **jednotného přihlašování** . Všimněte si, že pokud se vaše aplikace vytvořila na starém portálu, nemusí se tyto možnosti zobrazit.

Na této stránce se zobrazí také jedna možnost Další přihlášení: propojené přihlašování. Tuto možnost podporuje i proxy aplikace. Tato možnost však nepřidá do aplikace jednotné přihlašování. To, že tato aplikace už může mít jednotné přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services (AD FS). 

Tato možnost umožňuje správci vytvořit odkaz na aplikaci, kterou uživatelé poprvé dojdou při přístupu k aplikaci. Pokud je například aplikace konfigurovaná pro ověřování uživatelů pomocí Active Directory Federation Services (AD FS) 2,0, může správce pomocí možnosti "propojené přihlášení" vytvořit odkaz na přístupovém panelu.

## <a name="next-steps"></a>Další kroky
- [Trezor hesel pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Omezené delegování protokolu Kerberos pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování pomocí hlaviček pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md)
