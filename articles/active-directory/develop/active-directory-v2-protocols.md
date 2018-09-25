---
title: Další informace o povolení protokoly podporované službou Azure AD v2.0 | Dokumentace Microsoftu
description: Tento průvodce protokolů podporovaných koncového bodu Azure AD v2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4245cd4cf1f67007ced190e15d95929d854e303a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992741"
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>verze 2.0 protokolů: OAuth 2.0 a OpenID Connect

Koncový bod verze 2.0, můžete použít Azure AD pro identity-as-a-service pomocí standardní protokoly, OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, může být drobné rozdíly mezi implementacemi dvě z těchto protokolů. Zde uvedené informace budou užitečné, pokud budete chtít napište svůj kód přímo zasláním & zpracování HTTP požadavků, nebo použijte 3. stran opensourcovou knihovnu, spíše než pomocí jedné z našich [otevřít zdroj knihovny](reference-v2-libraries.md).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány bodem v2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

V téměř všechny toky OAuth a OpenID Connect existují čtyři strany součástí exchange:

![Role OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Autorizační Server** je koncový bod verze 2.0. Zodpovídá za zajištění identitu uživatele, poskytování a odvolání přístupu k prostředkům a vydávání tokenů. Je také označován jako zprostředkovatele identity – bezpečně zpracovává nic, aby se informace o uživateli, jejich přístup a vztahy důvěryhodnosti mezi stranami ve toku.
* **Vlastníka prostředku** je obvykle koncový uživatel. Je stranu, která vlastní data a má možnost povolit třetími stranami pro přístup k této dat nebo prostředek.
* **Klienta OAuth** používá vaše aplikace, identifikovat podle jeho ID aplikace. Je obvykle stranu, která komunikuje koncový uživatel a požaduje tokeny od autorizačního serveru. Klient musí udělit oprávnění pro přístup k prostředku vlastníkem prostředku.
* **Server prostředků** je, ve kterém se nachází zdroj nebo data. Vztahy důvěryhodnosti serveru ověřování pro zabezpečené ověřování a autorizaci klienta OAuth a využívají access_tokens nosiče k zajištění toho, že lze udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikace
Každá aplikace, kterou používá koncový bod verze 2.0 bude muset registrovat u [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) předtím, než může komunikovat pomocí účtu OAuth nebo OpenID Connect. Proces registrace aplikace bude shromažďovat a přiřadit aplikaci několik hodnot:

* **Id aplikace** , který jednoznačně identifikuje vaši aplikaci
* A **identifikátor URI pro přesměrování** nebo **identifikátor balíčku** , který lze použít k cílení odpovědí zpět do vaší aplikace
* Pár dalších hodnot specifické pro scénář.

Pro další informace si přečtěte, jak [zaregistrovat aplikaci](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Koncové body

Po registraci aplikace komunikuje se službou Azure AD pomocí zasílání požadavků na koncový bod verze 2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Kde `{tenant}` může trvat jednu ze čtyř různých hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Umožňuje uživatelům s osobní účty Microsoft a pracovních nebo školních účtů ze služby Azure Active Directory pro přihlášení do aplikace. |
| `organizations` |Umožňuje jenom uživatelům s pracovních nebo školních účtů ze služby Azure Active Directory pro přihlášení do aplikace. |
| `consumers` |Umožňuje jenom uživatelům s osobní účty Microsoft (MSA) pro přihlášení do aplikace. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` |Umožňuje jenom uživatelům s pracovních nebo školních účtů z konkrétního tenanta Azure Active Directory pro přihlášení do aplikace. Lze použít buď popisný název tenanta Azure AD nebo identifikátor guid vašeho tenanta. |

Další informace o tom, jak pracovat s těmito koncovými body zvolte jeden z následujících typů konkrétní aplikace.

## <a name="tokens"></a>Tokeny

Implementace v2.0 OAuth 2.0 a OpenID Connect využívat rozsáhlé nosné tokeny, včetně nosné tokeny, které jsou reprezentovány jako tokeny Jwt. Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu "nosiče" je každá strana, která může představovat token. Když strana musí nejdřív ověřit s Azure AD pro příjem nosný token, pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používat nežádoucí osobou. I když některé tokeny zabezpečení má předdefinovaný mechanismus pro brání jejich používání neoprávněnými osobami, nosné tokeny nemají tento mechanismus a nutné přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen v nezašifrované podobě, man-in střední útoku je možné škodlivý stranou získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem. Další informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](http://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typů tokeny použité v koncový bod verze 2.0 je k dispozici v [token reference koncového bodu v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připravení vidět některé příklad žádosti, začněte s některou z následující kurzy. Každé z nich odpovídá konkrétní ověřovacím scénáři. Pokud potřebujete pomoc při rozhodování, což je pravý tok pro vás, podívejte se [typy aplikací můžete vytvořit s využitím v2.0](v2-app-types.md).

* [Vytvoření mobilní a nativní aplikace s OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Vytvářejte webové aplikace pomocí Open ID Connect](v2-protocols-oidc.md)
* [Sestavení jednostránkové aplikace s OAuth 2.0 implicitní tok](v2-oauth2-implicit-grant-flow.md)
* [Sestavení démoni nebo serverové straně procesy s tok OAuth 2.0 pro přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md)
* [Získat tokeny ve webovém rozhraní API s tok OAuth 2.0 on-behalf-of](v2-oauth2-on-behalf-of-flow.md)
