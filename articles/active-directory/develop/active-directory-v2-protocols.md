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
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4c9e3c689a7b61e10d63272dd80815b11cacc71e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075412"
---
# <a name="v20-protocols---oauth-20-and-openid-connect"></a>verze 2.0 protokolů: OAuth 2.0 a OpenID Connect

Koncový bod verze 2.0, můžete použít Azure Active Directory (Azure AD) pro identity-as-a-service pomocí standardní protokoly, OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, může být drobné rozdíly mezi implementacemi dvě z těchto protokolů. Zde uvedené informace budou užitečné, pokud budete chtít napsat kód tak, že přímo, odesílání a zpracování žádostí HTTP nebo pomocí open source knihovnu třetí strany místo pomocí jedné z našich [knihovny open-source](reference-v2-libraries.md).

> [!NOTE]
> Ne všechny funkce a scénáře služby Azure AD jsou podporovány koncovým bodem verze 2.0. Pokud chcete zjistit, pokud je vhodné použít koncový bod verze 2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

V téměř všechny toky OAuth 2.0 a OpenID Connect existují čtyři strany součástí exchange:

![Role OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Autorizační Server** je koncovým bodem v2.0 a za zajištění identitu uživatele, poskytování a odvolání přístupu k prostředkům a vydávání tokenů. Autorizační server označované také jako zprostředkovatele identity – bezpečně zpracovává nic, aby se informace o uživateli, jejich přístup a vztahy důvěryhodnosti mezi stranami v toku.
* **Vlastníka prostředku** je obvykle koncového uživatele. Je stranu, která vlastní data a má možnost povolit třetími stranami pro přístup k této dat nebo prostředek.
* **Klienta OAuth** používá vaše aplikace, identifikovat podle jeho ID aplikace. Klient OAuth je obvykle stranu, která koncový uživatel komunikuje s a požaduje tokeny od autorizačního serveru. Klient musí udělit oprávnění pro přístup k prostředku vlastníkem prostředku.
* **Server prostředků** je, ve kterém se nachází zdroj nebo data. Vztahy důvěryhodnosti serveru ověřování pro zabezpečené ověřování a autorizaci klienta OAuth a využívají access_tokens nosiče k zajištění toho, že lze udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikace
Každá aplikace, kterou používá koncový bod verze 2.0 musí být zaregistrovaný ve [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) nebo prostřednictvím nového **registrace aplikací (Preview)** dojít [webu Azure portal](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) předtím, než může komunikovat pomocí účtu OAuth nebo OpenID Connect. Proces registrace aplikace bude shromažďovat a přiřadit aplikaci několik hodnot:

* **ID aplikace** , který jednoznačně identifikuje vaši aplikaci
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
| `common` | Umožňuje uživatelům s osobní účty Microsoft a pracovních nebo školních účtů ze služby Azure AD pro přihlášení do aplikace. |
| `organizations` | Umožňuje jenom uživatelům s pracovních nebo školních účtů ze služby Azure AD pro přihlášení do aplikace. |
| `consumers` | Umožňuje jenom uživatelům s osobní účty Microsoft (MSA) pro přihlášení do aplikace. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Umožňuje pouze uživatelé s pracovní nebo školní účty z konkrétní služby Azure AD tenanta pro přihlášení do aplikace. Lze použít buď popisný název tenanta Azure AD nebo identifikátor GUID vašeho tenanta. |

Chcete-li zjistěte, jak pracovat s těmito koncovými body, zvolte typ konkrétní aplikace v [protokoly](#protocols) části a přejděte na odkaz Další informace.

## <a name="tokens"></a>Tokeny

Implementace v2.0 OAuth 2.0 a OpenID Connect využívat rozsáhlé nosné tokeny, včetně nosné tokeny, které jsou reprezentovány jako tokeny Jwt. Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu "nosiče" je každá strana, která může představovat token. Když strana musí nejdřív ověřit s Azure AD pro příjem nosný token, pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používat nežádoucí osobou. I když některé tokeny zabezpečení má předdefinovaný mechanismus pro brání jejich používání neoprávněnými osobami, nosné tokeny nemají tento mechanismus a nutné přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen v nezašifrované podobě, můžete použít škodlivý stran útok man-in-the-middle získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem. Další informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](https://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typů tokeny použité v koncový bod verze 2.0 je k dispozici v [token reference koncového bodu v2.0](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připravení vidět některé příklad žádosti, začněte s některou z následující kurzy. Každé z nich odpovídá konkrétní ověřovacím scénáři. Pokud potřebujete pomoc při rozhodování, což je pravý tok pro vás, podívejte se [typy aplikací můžete vytvořit s využitím v2.0](v2-app-types.md).

* [Vytvoření mobilní a nativní aplikace s OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Tvořte webové aplikace s OpenID Connect](v2-protocols-oidc.md)
* [Sestavení jednostránkové aplikace s OAuth 2.0 implicitní tok](v2-oauth2-implicit-grant-flow.md)
* [Sestavení démoni nebo serverové procesy pomocí toku přihlašovacích údajů klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Získat tokeny ve webovém rozhraní API s tok OAuth 2.0 on-behalf-of](v2-oauth2-on-behalf-of-flow.md)
