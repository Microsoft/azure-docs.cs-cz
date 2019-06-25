---
title: Další informace o povolení protokolů nepodporuje platforma identit Microsoft | Azure
description: Tento průvodce protokoly OAuth 2.0 a OpenID Connect, které jsou podporovány bodem Microsoft identity platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfc9c027806cb1a3f65e67eda771894a7250ee67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417652"
---
# <a name="microsoft-identity-platform-protocols"></a>Protokoly Microsoft identity platform

Microsoft identity platform koncový bod pro identity-as-a-service pomocí standardní protokoly, OpenID Connect a OAuth 2.0. Služba je kompatibilní se standardy, může být drobné rozdíly mezi implementacemi dvě z těchto protokolů. Zde uvedené informace budou užitečné, pokud budete chtít napsat kód tak, že přímo, odesílání a zpracování žádostí HTTP nebo pomocí open source knihovnu třetí strany místo pomocí jedné z našich [knihovny open-source](reference-v2-libraries.md).

> [!NOTE]
> Ne všechny služby Azure AD scénáře a funkce jsou podporovány bodem platforma identit Microsoft. Chcete-li zjistit, zda by měl používat Microsoft identity platform endpoint, přečtěte si informace o [Microsoft identity platform omezení](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

V téměř všechny toky OAuth 2.0 a OpenID Connect existují čtyři strany součástí exchange:

![Role OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Autorizační Server** je koncový bod Microsoft identity platform a za zajištění identitu uživatele, poskytování a odvolání přístupu k prostředkům a vydávání tokenů. Autorizační server označované také jako zprostředkovatele identity – bezpečně zpracovává nic, aby se informace o uživateli, jejich přístup a vztahy důvěryhodnosti mezi stranami v toku.
* **Vlastníka prostředku** je obvykle koncového uživatele. Je stranu, která vlastní data a má možnost povolit třetími stranami pro přístup k této dat nebo prostředek.
* **Klienta OAuth** používá vaše aplikace, identifikovat podle jeho ID aplikace. Klient OAuth je obvykle stranu, která koncový uživatel komunikuje s a požaduje tokeny od autorizačního serveru. Klient musí udělit oprávnění pro přístup k prostředku vlastníkem prostředku.
* **Server prostředků** je, ve kterém se nachází zdroj nebo data. Vztahy důvěryhodnosti serveru ověřování pro zabezpečené ověřování a autorizaci klienta OAuth a využívají přístupových tokenů nosiče k zajištění toho, že lze udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikace

Každá aplikace, kterou chce, aby se tak, aby přijímal jak osobní i pracovní nebo školní účty musí být registrovaný prostřednictvím metody **registrace aplikací** dojít [webu Azure portal](https://aka.ms/appregistrations) předtím, než mohl tyto uživatele ve službě pomocí OAuth 2.0 nebo OpenID Connect. Proces registrace aplikace bude shromažďovat a přiřadit aplikaci několik hodnot:

* **ID aplikace** , který jednoznačně identifikuje vaši aplikaci
* A **identifikátor URI pro přesměrování** (volitelné), který lze použít k cílení odpovědí zpět do vaší aplikace
* Pár dalších hodnot specifické pro scénář.

Pro další informace si přečtěte, jak [zaregistrovat aplikaci](quickstart-register-app.md).

## <a name="endpoints"></a>Koncové body

Po registraci aplikace komunikuje s platformou identity Microsoft pomocí zasílání požadavků na koncový bod:

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

> [!TIP]
> Libovolná aplikace zaregistrované ve službě Azure AD můžete použít Microsoft identity platform koncového bodu, i v případě, že již příště přihlásí osobní účty.  Tímto způsobem můžete migrovat existující aplikace, aby platforma identit Microsoft a [MSAL](reference-v2-libraries.md) bez nutnosti znovu vytvářet aplikace.  

## <a name="tokens"></a>Tokeny

Microsoft identity platform provádění OAuth 2.0 a OpenID Connect využívat rozsáhlé nosné tokeny, včetně nosné tokeny, které jsou reprezentovány jako tokeny Jwt. Nosný token je token zjednodušené zabezpečení, která uděluje "nosiče" přístup k chráněnému prostředku. V tomto smyslu "nosiče" je každá strana, která může představovat token. Když strana musí nejdřív ověřit s platformou identity Microsoft získat nosný token, pokud nejsou požadované kroky k zabezpečení token v přenos a ukládání, můžete zachytit a používat nežádoucí osobou. I když některé tokeny zabezpečení má předdefinovaný mechanismus pro brání jejich používání neoprávněnými osobami, nosné tokeny nemají tento mechanismus a nutné přenášet v zabezpečený kanál, jako je zabezpečení transportní vrstvy (HTTPS). Pokud nosný token je přenesen v nezašifrované podobě, můžete použít škodlivý stran útok man-in-the-middle získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny bezpečným způsobem. Další informace o zabezpečení na nosné tokeny, naleznete v tématu [5 část dokumentu RFC 6750](https://tools.ietf.org/html/rfc6750).

Další podrobnosti tokeny použité v Microsoft identity platform koncový bod je k dispozici v různých typů [Microsoft identity platform reference koncového bodu tokenu](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připravení vidět některé příklad žádosti, začněte s některou z následující kurzy. Každé z nich odpovídá konkrétní ověřovacím scénáři. Pokud potřebujete pomoc při rozhodování, což je pravý tok pro vás, podívejte se [typy aplikací můžete sestavit s platformou identity Microsoft](v2-app-types.md).

* [Vytvoření mobilní a nativní aplikace s OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Tvořte webové aplikace s OpenID Connect](v2-protocols-oidc.md)
* [Sestavení jednostránkové aplikace s OAuth 2.0 implicitní tok](v2-oauth2-implicit-grant-flow.md)
* [Sestavení démoni nebo serverové procesy pomocí toku přihlašovacích údajů klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Získat tokeny ve webovém rozhraní API s tok OAuth 2.0 on-behalf-of](v2-oauth2-on-behalf-of-flow.md)
