---
title: Konfigurace relace a jednotného přihlašování
titleSuffix: Azure AD B2C
description: Konfigurace relace a jednotného přihlašování (SSO) v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950064"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Konfigurace relace a jednotného přihlašování v Azure Active Directory B2C

Tato funkce poskytuje jemně odstupňovaný ovládací prvek na [základě toku pro jednotlivé uživatele](active-directory-b2c-reference-policies.md):

- Životnost relací webových aplikací, které jsou spravovány Azure AD B2C.
- Chování jednotného přihlašování (SSO) napříč několika aplikacemi a toky uživatelů ve vašem Azure AD B2C tenantovi.

## <a name="session-behavior"></a>Chování relace

Azure AD B2C podporuje [ověřovací protokol OpenID Connect](active-directory-b2c-reference-oidc.md) pro povolení zabezpečeného přihlašování k webovým aplikacím. Pomocí následujících vlastností můžete spravovat relace webových aplikací:

- **Doba života relace webové aplikace (minuty)** – doba života souboru cookie relace Azure AD B2C's, která se po úspěšném ověření uloží v prohlížeči uživatele.
    - Výchozí hodnota = 1440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximum (včetně) = 1440 minut.
- **Časový limit relace webové aplikace** – Pokud je tento přepínač nastavený na **absolutní**, uživatel se po uplynutí časového intervalu zadaného v době **Trvání relace webové aplikace (minuty)** bude muset znovu ověřit. Pokud je tento přepínač nastavený na **vracení** (výchozí nastavení), zůstane uživatel přihlášený, dokud bude uživatel ve vaší webové aplikaci neustále aktivní.

Následující případy použití jsou povolené pomocí těchto vlastností:

- Splnění požadavků na zabezpečení a dodržování předpisů v oboru nastavením příslušných životností relací webových aplikací.
- Vynutí ověřování po nastaveném časovém období během interakce uživatele s vysokou zabezpečením části vaší webové aplikace.

Tato nastavení nejsou k dispozici pro uživatelské toky resetování hesla.

## <a name="single-sign-on-sso-configuration"></a>Konfigurace jednotného přihlašování (SSO)

Pokud máte ve svém tenantovi B2C více aplikací a toků uživatelů, můžete pomocí vlastnosti **Konfigurace jednotného přihlašování** spravovat interakce uživatelů napříč nimi. Vlastnost můžete nastavit na jedno z následujících nastavení:

- **Tenant** – toto nastavení je výchozí. Pomocí tohoto nastavení může více aplikací a uživatelských toků v tenantovi B2C sdílet stejnou relaci uživatele. Když se například uživatel přihlásí do aplikace, může se při přístupu uživatele bez problémů přihlásit k jiné společnosti Contoso farmacie.
- **Aplikace** – toto nastavení umožňuje udržovat uživatelskou relaci exkluzivně pro aplikaci, nezávisle na jiných aplikacích. Například pokud chcete, aby se uživatel přihlásil do společnosti Contoso Farmaci (se stejnými přihlašovacími údaji), i když je uživatel již přihlášen k nákupu společnosti Contoso, jiná aplikace ve stejném tenantovi B2C.
- **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro uživatelský tok, a to nezávisle na aplikacích, které ji používají. Pokud se například uživatel už přihlásil a dokončil krok vícefaktorového ověřování (MFA), může se uživateli udělit přístup k několika částem aplikace s vyšším zabezpečením, pokud relace vázaná na tok uživatele nevyprší.
- **Disabled** – toto nastavení vynutí, aby uživatel při každém spuštění zásady spouštěl celý tok uživatele.

Tato nastavení nejsou k dispozici pro uživatelské toky resetování hesla.

