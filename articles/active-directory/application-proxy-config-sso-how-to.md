---
title: Postup konfigurace jednotného přihlašování k aplikaci Proxy aplikace | Microsoft Docs
description: Jak můžete nakonfigurovat jednotné přihlašování k vaší aplikaci proxy aplikace rychle
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 3d7711ac62ebc870b98e2f2696a74f236c2d6a88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591476"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Postup konfigurace jednotného přihlašování k aplikaci Proxy aplikace

Jednotné přihlašování (SSO) umožňuje uživatelům přístup k aplikaci bez ověřování vícekrát. Ji umožňuje ověření jednotného proběhnout v cloudu s Azure Active Directory a umožňuje služba nebo konektor zosobnění uživatele dokončit všechny další ověřování výzvy z aplikace.

## <a name="how-to-configure-single-sign-on"></a>Postup konfigurace na jednotné přihlašování
Pokud chcete nakonfigurovat jednotné přihlašování, nejprve ujistěte se, že je vaše aplikace nakonfigurovaná pro předběžné ověření pomocí služby Azure Active Directory. Chcete-li provést tuto konfiguraci, přejděte na **Azure Active Directory**  - &gt; **podnikové aplikace, které**  - &gt; **všechny aplikace**   - &gt; Aplikace  **- &gt; Proxy aplikace**. Na této stránce naleznete v poli "Předběžné ověřování" a ujistěte se, který je nastaven na "Azure Active Directory. 

Další informace o předběžné ověření metody, najdete v části Krok 4 [publikování dokumentu aplikace](manage-apps/application-proxy-publish-azure-portal.md).

   ![Metoda předběžného ověřování na portálu Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurace režimy přihlašování pro aplikace Proxy aplikace
Nakonfigurujte konkrétní typ jednotného přihlašování. Metody přihlašování jsou klasifikovány podle jaký typ ověřování back-end aplikace používá. Proxy aplikace aplikace podporují tři typy přihlašování:

-   **Založené na heslech přihlašování**: založené na heslech přihlášení lze použít pro všechny aplikace, která používá pole uživatelské jméno a heslo k přihlášení. Postup konfigurace je v [heslo SSO konfigurace dokumentaci](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Integrované ověřování systému Windows**: pro aplikace pomocí integrovaného ověřování systému Windows (IWA), jednotné přihlašování je povolené prostřednictvím použitím protokolu Kerberos omezené delegování (KCD). Tato metoda poskytuje oprávnění konektory Proxy aplikace ve službě Active Directory k zosobnění uživatelů a odesílat a přijímat tokeny jejich jménem. Podrobné informace o konfiguraci použitím KCD najdete v [jednotné přihlašování s použitím KCD dokumentaci](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Na základě záhlaví přihlašování**: na základě záhlaví přihlášení je povolená Díky partnerství a vyžadovat další konfiguraci. Podrobnosti o partnerství a podrobné pokyny pro konfiguraci jednotné přihlašování pro aplikace, která používá hlavičky pro ověřování najdete v tématu [PingAccess dokumentaci k Azure AD](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md).

Každá z těchto možností najdete tak, že přejdete do vaší aplikace v "Podnikové aplikace" a otevírání **jednotné přihlašování** stránky v levé nabídce. Všimněte si, že pokud vaše aplikace byla vytvořena na původním portálu, se nemusí zobrazit všechny tyto možnosti.

Na této stránce můžete také najdete v jednom další možnost přihlášení: propojené přihlášení. Tato možnost je také podporována službou Proxy aplikací. Ale tuto možnost nepřidá jednotného přihlašování k aplikaci. Ale nutné dodat, že aplikace už můžete mít jednotné přihlašování implementovaná pomocí jiné služby, jako je Active Directory Federation Services. 

Tato možnost umožňuje správce vytvořit odkaz na aplikaci první krajině tohoto uživatele na při přístupu k aplikaci. Například pokud je aplikace, který je nakonfigurovaný k ověřování uživatelů pomocí Active Directory Federation Services 2.0, správce slouží možnost "propojené přihlášení" k vytvoření odkazu na na panel přístupu.

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
