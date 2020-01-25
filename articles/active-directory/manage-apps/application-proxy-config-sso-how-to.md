---
title: Jak nakonfigurovat jednotné přihlašování k aplikaci proxy aplikace
description: Jak nakonfigurovat jednotné přihlašování pro vaši aplikaci proxy aplikací rychle
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712012"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak nakonfigurovat jednotné přihlašování k aplikaci Proxy aplikací

Jednotné přihlašování (SSO) umožňuje vašim uživatelům přístup k aplikaci bez ověřování více než jednou. Umožňuje ověření jednotného vyskytuje v cloudu s Azure Active Directory a umožňuje služba nebo konektor zosobnit uživatele dokončit všechny další ověřovací výzvy z aplikace.

## <a name="how-to-configure-single-sign-on"></a>Postup konfigurace jednotného přihlašování na
Pokud chcete nakonfigurovat jednotné přihlašování, nejprve ujistěte se, že vaše aplikace je nakonfigurovaná pro předběžné ověření přes Azure Active Directory. Chcete-li provést tuto konfiguraci, přejděte na **Azure Active Directory**  - &gt; **podnikové aplikace**  - &gt; **všechny aplikace**   - &gt; Aplikace  **- &gt; Proxy aplikací**. Na této stránce najdete v poli "Předběžné ověření" a ujistěte se, že je nastavena na "Azure Active Directory. 

Další informace o metodách předběžné ověření, najdete v kroku 4 [publikování dokumentu aplikace](application-proxy-add-on-premises-application.md).

   ![Metoda předběžného ověřování služby na webu Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimů jednotné přihlašování pro aplikaci Proxy aplikací
Nakonfigurujte konkrétní typ jednotného přihlašování. Metody přihlašování mají klasifikaci založené na jaký typ ověřování back-endu aplikace používá. Aplikace Proxy aplikací podporují tři typy přihlašování:

-   **Přihlašování na základě heslo**: přihlašování na základě hesla je možné pro každou aplikaci, která používá pole uživatelské jméno a heslo k přihlášení. Postup konfigurace je v [konfigurace hesla jednotného přihlašování pro aplikaci Galerie Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrované ověřování Windows**: pro aplikace používající integrované ověřování Windows (IWA), jednotné přihlašování je povolená prostřednictvím protokolu Kerberos omezené delegování (KCD). Tato metoda poskytuje oprávnění konektory Proxy aplikací služby Active Directory zosobňovalo a odesílat a přijímat tokeny jejich jménem. Podrobnosti o tom, jak nakonfigurovat KCD najdete v [jednotné přihlašování s KCD dokumentaci](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Přihlašování na základě záhlaví**: přihlašování na základě hlaviček je povolená Díky partnerství a vyžaduje určitou další konfiguraci. Podrobnosti o partnerství a podrobné pokyny pro konfiguraci jednotného přihlašování k aplikaci, která používá hlavičky pro ověřování najdete v tématu [PingAccess pro dokumentaci k Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Jednotné přihlašování SAML**: s jednotným přihlašováním SAML se Azure AD ověřuje do aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje informace přihlašování k aplikaci pomocí připojení protokolu. Pomocí jednotného přihlašování založené na SAML můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML. Informace o nastavení jednotného přihlašování SAML najdete v tématu [SAML pro jednotné přihlašování pomocí proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Každá z těchto možností najdete tak, že přejdete do vaší aplikace v oddílu "Podnikové aplikace" a otevírání **Single Sign-On** stránky v nabídce vlevo. Všimněte si, že pokud se vaše aplikace vytvořila na starém portálu, nemusí se tyto možnosti zobrazit.

Na této stránce se také zobrazí jedna další možnosti přihlašování: propojené přihlašování. Tato možnost je také podporována službou Proxy aplikací. Ale tato možnost nepřidá jednotné přihlašování do aplikace. Ale nutné dodat, že aplikace už můžete mít jednotného přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services. 

Tato možnost umožňuje správce pro vytvoření odkazu na aplikaci první pozemního tohoto uživatele na při přístupu k aplikaci. Například pokud je aplikace, která je nakonfigurovaná k ověřování uživatelů pomocí služby Active Directory Federation Services 2.0, správce použít možnost "propojené přihlašování" pro vytvoření odkazu na něj na přístupovém panelu.

## <a name="next-steps"></a>Další kroky
- [Trezor hesel pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Omezené delegování protokolu Kerberos pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)
- [Ověřování pomocí hlaviček pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md)
