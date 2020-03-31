---
title: Jak nakonfigurovat jednotné přihlašování k aplikaci Proxy aplikace
description: Jak můžete rychle nakonfigurovat jednotné přihlašování k aplikaci proxy aplikace
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712012"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Konfigurace jednotného přihlášení k aplikaci Proxy aplikace

Jednotné přihlašování (SSO) umožňuje uživatelům přístup k aplikaci bez ověřování vícekrát. Umožňuje jediné ověřování dojít v cloudu, proti Službě Azure Active Directory a umožňuje službě nebo konektoru zosobnit uživatele k dokončení další problémy ověřování z aplikace.

## <a name="how-to-configure-single-sign-on"></a>Jak nakonfigurovat jednotné přihlašování
Chcete-li nakonfigurovat automatické přihlašovat, nejprve se ujistěte, že vaše aplikace je nakonfigurovaná pro předběžné ověřování prostřednictvím služby Azure Active Directory. Chcete-li provést tuto konfiguraci, přejděte na **Azure Active Directory**  - &gt; **Enterprise Applications**  - &gt; **Všechny aplikace**  - &gt; Proxy ** - &gt; aplikace**aplikace . Na této stránce se zobrazí pole Předběžné ověření a ujistěte se, že je nastavena na "Azure Active Directory. 

Další informace o metodách předběžného ověřování naleznete v kroku 4 [dokumentu pro publikování aplikací](application-proxy-add-on-premises-application.md).

   ![Metoda předběžného ověřování na webu Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimů jednotného přihlašování pro aplikace proxy aplikací
Nakonfigurujte konkrétní typ jednotného přihlášení. Metody přihlášení jsou klasifikovány na základě typu ověřování, který používá back-endová aplikace. Aplikace proxy aplikací podporují tři typy přihlášení:

-   **Přihlášení založené na heslech**: Přihlašování pomocí hesla lze použít pro libovolnou aplikaci, která k přihlášení používá pole uživatelského jména a hesla. Kroky konfigurace najdete v [části Konfigurace hesla Jednotné přihlašování pro aplikaci galerie Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrované ověřování systému Windows:** U aplikací používajících integrované ověřování systému Windows (IWA) je jednotné přihlašování povoleno prostřednictvím delegování omezeného protokolu Kerberos (KCD). Tato metoda uděluje konektorům proxy aplikací oprávnění ve službě Active Directory zosobnit uživatele a odesílat a přijímat tokeny jejich jménem. Podrobnosti o konfiguraci KCD naleznete v [jednotném přihlášení s dokumentací KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Přihlášení založené na záhlaví**: Přihlášení založené na záhlaví je povoleno prostřednictvím partnerství a vyžaduje určitou další konfiguraci. Podrobnosti o partnerství a podrobné pokyny pro konfiguraci jednotného přihlášení k aplikaci, která používá záhlaví pro ověřování, naleznete v [dokumentaci PingAccess pro Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Jednotné přihlašování SAML**: S jedním přihlašováním SAML se Azure AD ověřuje k aplikaci pomocí účtu Azure AD uživatele. Azure AD sděluje přihlašovací informace do aplikace prostřednictvím protokolu připojení. S jediným přihlašování saml můžete mapovat uživatele na konkrétní role aplikací na základě pravidel, která definujete v deklaracích SAML. Informace o nastavení jednotného přihlašování SAML naleznete v tématu [SAML pro jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-on-premises-apps.md).

Každou z těchto možností lze nalézt tak, že přejdete do aplikace v "Enterprise Applications" a otevřete stránku **jednotného přihlášení** v levé nabídce. Všimněte si, že pokud vaše aplikace byla vytvořena ve starém portálu, nemusí zobrazit všechny tyto možnosti.

Na této stránce se také zobrazí jedna další možnost přihlášení: Propojené přihlášení. Tato možnost je také podporována proxy aplikací. Tato možnost však nepřidá jednotné přihlašování do aplikace. To znamená, že aplikace již může mít jednotné přihlašování implementováno pomocí jiné služby, jako je například služba Active Directory Federation Services. 

Tato možnost umožňuje správci vytvořit odkaz na aplikaci, na kterou uživatelé při přístupu k aplikaci nejprve přistanou. Pokud je například aplikace nakonfigurována k ověřování uživatelů pomocí služby Active Directory Federation Services 2.0, může správce pomocí možnosti Propojené přihlášení vytvořit odkaz na přístupový panel.

## <a name="next-steps"></a>Další kroky
- [Trezor hesel pro jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos Omezené delegování pro jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování založené na záhlaví pro jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML pro jednotné přihlášení pomocí proxy aplikace](application-proxy-configure-single-sign-on-on-premises-apps.md).
