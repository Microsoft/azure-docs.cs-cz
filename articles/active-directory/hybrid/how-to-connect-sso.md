---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování | Dokumenty společnosti Microsoft'
description: Toto téma popisuje Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování a jak umožňuje poskytovat skutečné jednotné přihlašování pro podnikové uživatele stolních počítačů v rámci podnikové sítě.
services: active-directory
keywords: co je Azure AD Connect, nainstalujte službu Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483750"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Bezproblémové jednotné přihlašování služby Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Co je bezproblémové jednotné přihlašování služby Azure Active Directory?

Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. Pokud je povoleno, uživatelé nemusí zadávat svá hesla pro přihlášení k Azure AD a obvykle dokonce zadejte svá uživatelská jména. Tato funkce poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez potřeby dalších místních komponent.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Bezproblémové jednotné přihlašování lze kombinovat s [synchronizací hash hesla](how-to-connect-password-hash-synchronization.md) nebo [předávací ověřování](how-to-connect-pta.md) přihlašovací metody. Bezproblémové jednotné přihlašované služby _se nevztahuje_ na službu ADFS (Active Directory Federation Services).

![Bezproblémové jednotné přihlašování](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Bezproblémové jednotné přihlašování potřebuje zařízení uživatele, aby se připojil pouze k **doméně,** ale nepoužívá se na [azure ad připojená](../devices/concept-azure-ad-join.md) zařízení nebo [hybridní Azure AD připojenzařízení.](../devices/concept-azure-ad-join-hybrid.md) Připojovací služby ve službě Azure AD se připojily a hybridní Azure AD připojil funguje na základě [primární hotokenu aktualizace](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Klíčové výhody

- *Skvělé uživatelské prostředí*
  - Uživatelé jsou automaticky přihlášeni k místním i cloudovým aplikacím.
  - Uživatelé nemusí zadávat svá hesla opakovaně.
- *Snadné nasazení & správa*
  - Žádné další součásti potřebné v místním prostředí, aby to fungovalo.
  - Funguje s libovolnou metodou cloudového ověřování – [synchronizace hash hesel](how-to-connect-password-hash-synchronization.md) nebo [předávací ověřování](how-to-connect-pta.md).
  - Lze použít pro některé nebo všechny uživatele pomocí zásad skupiny.
  - Zaregistrujte zařízení, která nejsou windows 10, pomocí Azure AD bez nutnosti jakékoli infrastruktury služby AD FS. Tato funkce vyžaduje použití verze 2.1 nebo novější [klienta připojení k pracovišti](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Zvýraznění funkcí

- Přihlašovací uživatelské jméno může být místní výchozí uživatelské`userPrincipalName`jméno ( ) nebo jiný atribut`Alternate ID`nakonfigurovaný v Azure AD Connect ( ). Oba případy použití fungují, protože `securityIdentifier` bezproblémové jednotné přihlašující používá deklarace v lístku protokolu Kerberos vyhledat odpovídající objekt uživatele ve službě Azure AD.
- Bezproblémové jednotné přihlašovatosti je oportunistické funkce. Pokud se z nějakého důvodu nezdaří, prostředí pro přihlášení uživatele se vrátí k jeho pravidelnému chování - tj.
- Pokud aplikace (například `https://myapps.microsoft.com/contoso.com`) předá parametr `domain_hint` (OpenID Connect) nebo `whr` (SAML) `login_hint` – identifikaci vašeho klienta nebo parametr – identifikaci uživatele v žádosti o přihlášení azure ad, uživatelé jsou automaticky přihlášeni, aniž by zadali uživatelská jména nebo hesla.
- Uživatelé také získat tiché přihlašovací prostředí, pokud `https://contoso.sharepoint.com`aplikace (například) odešle požadavky na přihlášení do koncových bodů Azure `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` AD nastavených jako klienti – to znamená, nebo - namísto společného koncového bodu Azure AD - to znamená `https://login.microsoftonline.com/common/<...>`.
- Odhlášení je podporováno. To umožňuje uživatelům zvolit jiný účet Azure AD pro přihlášení, místo toho, aby se automaticky přihlásili pomocí bezproblémového jednotného přihlašování.
- Klienti Office 365 Win32 (Outlook, Word, Excel a další) s verzemi 16.0.8730.xxxx a vyššími jsou podporováni pomocí neinteraktivního toku. Pro OneDrive budete muset aktivovat [funkci tiché konfigurace OneDrivu](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) pro tiché přihlašování.
- Dá se povolit prostřednictvím služby Azure AD Connect.
- Jedná se o bezplatnou funkci a k jeho použití nepotřebujete žádné placené edice Azure AD.
- Je podporován a klienty webového prohlížeče a klienty sady Office, kteří podporují [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) na platformách a prohlížečích schopných ověřování protokolem Kerberos:

| OS\Prohlížeč |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Ano\*|Ano|Ano|Ano\*\*\*|Není dostupné.
|Windows 8.1|Ano\*|Není dostupné.|Ano|Ano\*\*\*|Není dostupné.
|Windows 8|Ano\*|Není dostupné.|Ano|Ano\*\*\*|Není dostupné.
|Windows 7|Ano\*|Není dostupné.|Ano|Ano\*\*\*|Není dostupné.
|Windows Server 2012 R2 nebo vyšší|Ano\*\*|Není dostupné.|Ano|Ano\*\*\*|Není dostupné.
|Mac OS X|Není dostupné.|Není dostupné.|Ano\*\*\*|Ano\*\*\*|Ano\*\*\*


\*Vyžaduje aplikaci Internet Explorer verze 10 nebo vyšší.

\*\*Vyžaduje aplikaci Internet Explorer verze 10 nebo vyšší. Zakázání rozšířeného chráněného režimu

\*\*\*Vyžaduje [další konfiguraci](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Pro Windows 10 doporučujeme použít [Azure AD Join](../devices/concept-azure-ad-join.md) pro optimální jednotné přihlašování pomocí Azure AD.

## <a name="next-steps"></a>Další kroky

- [**Rychlý start**](how-to-connect-sso-quick-start.md) – zprovoznění bezproblémového jednotného přihlašování Azure AD.
- [**Plán nasazení**](https://aka.ms/deploymentplans/sso) – podrobný plán nasazení.
- [**Technické Deep Dive**](how-to-connect-sso-how-it-works.md) - Pochopit, jak tato funkce funguje.
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) - Odpovědi na často kladené otázky.
- [**Poradce při potížích**](tshoot-connect-sso.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Pro podání nových žádostí o funkce.

