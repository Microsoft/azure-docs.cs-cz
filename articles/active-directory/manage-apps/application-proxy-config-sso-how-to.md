---
title: Jak nakonfigurovat jednotné přihlašování k aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Jak nakonfigurovat jednotné přihlašování pro vaši aplikaci proxy aplikací rychle
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: celested
ms.reviewer: asteen
ms.openlocfilehash: e12ca3f11053add2a4398f706a5e3b10e9e19610
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962549"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Jak nakonfigurovat jednotné přihlašování k aplikaci Proxy aplikací

Jednotné přihlašování (SSO) umožňuje vašim uživatelům přístup k aplikaci bez ověřování více než jednou. Umožňuje ověření jednotného vyskytuje v cloudu s Azure Active Directory a umožňuje služba nebo konektor zosobnit uživatele dokončit všechny další ověřovací výzvy z aplikace.

## <a name="how-to-configure-single-sign-on"></a>Postup konfigurace jednotného přihlašování na
Pokud chcete nakonfigurovat jednotné přihlašování, nejprve ujistěte se, že vaše aplikace je nakonfigurovaná pro předběžné ověření přes Azure Active Directory. Chcete-li provést tuto konfiguraci, přejděte na **Azure Active Directory**  - &gt; **podnikové aplikace**  - &gt; **všechny aplikace**   - &gt; Aplikace  **- &gt; Proxy aplikací**. Na této stránce najdete v poli "Předběžné ověření" a ujistěte se, že je nastavena na "Azure Active Directory. 

Další informace o metodách předběžné ověření, najdete v kroku 4 [publikování dokumentu aplikace](application-proxy-add-on-premises-application.md).

   ![Metoda předběžného ověřování služby na webu Azure portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimů jednotné přihlašování pro aplikaci Proxy aplikací
Nakonfigurujte konkrétní typ jednotného přihlašování. Metody přihlašování mají klasifikaci založené na jaký typ ověřování back-endu aplikace používá. Aplikace Proxy aplikací podporují tři typy přihlašování:

-   **Přihlašování na základě heslo**: Přihlašování na základě heslo, je možné pro každou aplikaci, která používá pole uživatelské jméno a heslo k přihlášení. Postup konfigurace je v [konfigurace hesla jednotného přihlašování pro aplikaci Galerie Azure AD](configure-password-single-sign-on-gallery-applications.md).

-   **Integrované ověřování Windows**: Pro aplikace používající integrované ověřování Windows (IWA) jednotné přihlašování je povolená prostřednictvím protokolu Kerberos omezené delegování (KCD). Tato metoda poskytuje oprávnění konektory Proxy aplikací služby Active Directory zosobňovalo a odesílat a přijímat tokeny jejich jménem. Podrobnosti o tom, jak nakonfigurovat KCD najdete v [jednotné přihlašování s KCD dokumentaci](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Přihlašování na základě záhlaví**: Přihlašování na základě hlaviček je povolená Díky partnerství a vyžaduje určitou další konfiguraci. Podrobnosti o partnerství a podrobné pokyny pro konfiguraci jednotného přihlašování k aplikaci, která používá hlavičky pro ověřování najdete v tématu [PingAccess pro dokumentaci k Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

Každá z těchto možností najdete tak, že přejdete do vaší aplikace v oddílu "Podnikové aplikace" a otevírání **Single Sign-On** stránky v nabídce vlevo. Všimněte si, že pokud vaše aplikace byla vytvořena na starém portálu, nemusí zobrazit všechny tyto možnosti.

Na této stránce se také zobrazí jedna další možnosti přihlašování: Propojené přihlašování. Tato možnost je také podporována službou Proxy aplikací. Ale tato možnost nepřidá jednotné přihlašování do aplikace. Ale nutné dodat, že aplikace už můžete mít jednotného přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services. 

Tato možnost umožňuje správce pro vytvoření odkazu na aplikaci první pozemního tohoto uživatele na při přístupu k aplikaci. Například pokud je aplikace, která je nakonfigurovaná k ověřování uživatelů pomocí služby Active Directory Federation Services 2.0, správce použít možnost "propojené přihlašování" pro vytvoření odkazu na něj na přístupovém panelu.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)
