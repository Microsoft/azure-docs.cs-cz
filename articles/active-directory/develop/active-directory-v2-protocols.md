---
title: Protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Průvodce pro protokoly OAuth 2,0 a OpenID Connect, které podporuje koncový bod Microsoft Identity Platform.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0bb7812d75fa3276b52a182f9184e28a21a910ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83737482"
---
# <a name="oauth-20-and-openid-connect-protocols-on-microsoft-identity-platform"></a>Protokoly OAuth 2,0 a OpenID Connect na platformě Microsoft Identity Platform

Koncový bod Microsoft Identity Platform pro identitu identity jako službu implementuje ověřování a autorizaci s oborovým standardem protokolů OpenID Connect (OIDC) a OAuth 2,0, v uvedeném pořadí. I když je služba kompatibilní se standardy, můžou být mezi dvěma implementacemi těchto protokolů malé rozdíly. Informace zde budou užitečné, pokud se rozhodnete napsat kód přímým odesíláním a zpracováním požadavků HTTP nebo použitím open source knihovny od jiného výrobce namísto použití jedné z našich [Open Source knihoven](reference-v2-libraries.md).

## <a name="the-basics"></a>Základy

U téměř všech toků OAuth 2,0 a OpenID Connect se v systému Exchange účastní čtyři strany:

![Diagram znázorňující role OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Autorizační Server** je koncovým bodem platformy Microsoft Identity Platform a zodpovídá za to, aby uživatel mohl zajistit jeho identitu, udělení a odvolání přístupu k prostředkům a vydávání tokenů. Autorizační Server se také označuje jako poskytovatel identity – bez bezpečného zpracovává cokoli s informacemi uživatele, jejich přístupem a vztahy důvěryhodnosti mezi stranami v toku.
* **Vlastníkem prostředku** je obvykle koncový uživatel. Je to strana, která vlastní data a má oprávnění k tomu, aby třetím stranám umožnila přístup k těmto datům nebo prostředkům.
* **Klient OAuth** je vaše aplikace, identifikovaný identifikátorem jeho aplikace. Klient OAuth je obvykle strana, se kterou koncový uživatel komunikuje, a žádá o tokeny od autorizačního serveru. Klient musí mít udělené oprávnění pro přístup k prostředku vlastníkem prostředku.
* **Server prostředků** je místo, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru za účelem bezpečného ověřování a autorizace klienta OAuth a používá přístupové tokeny nosiče k zajištění toho, aby bylo možné udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikace

Každá aplikace, která chce přijmout osobní i pracovní nebo školní účty, musí být zaregistrovaná prostřednictvím **registrace aplikacího** prostředí v [Azure Portal](https://aka.ms/appregistrations) předtím, než bude moct uživatele podepsat pomocí OAuth 2,0 nebo OpenID Connect. Proces registrace aplikace bude shromažďovat a přiřazovat do vaší aplikace několik hodnot:

* **ID aplikace** , které jedinečně identifikuje vaši aplikaci
* **Identifikátor URI přesměrování** (volitelné), který se dá použít k přímému směrování odpovědí zpátky do vaší aplikace
* Několik dalších hodnot specifických pro konkrétní scénář.

Pro další informace si přečtěte, jak [zaregistrovat aplikaci](quickstart-register-app.md).

## <a name="endpoints"></a>Koncové body

Po registraci aplikace komunikuje s platformou Microsoft identity tím, že odešle požadavky na koncový bod:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Kde `{tenant}` může být jedna ze čtyř různých hodnot:

| Hodnota | Description |
| --- | --- |
| `common` | Umožňuje uživatelům, aby se do aplikace přihlásili pomocí osobních účtů Microsoft a pracovních/školních účtů z Azure AD. |
| `organizations` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s pracovními nebo školními účty ze služby Azure AD. |
| `consumers` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s osobními účty Microsoft (MSA). |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s pracovními nebo školními účty z konkrétního tenanta Azure AD. Dá se použít popisný název domény tenanta Azure AD nebo identifikátor GUID klienta. |

Pokud se chcete dozvědět, jak s těmito koncovými body pracovat, vyberte v části [protokoly](#protocols) konkrétní typ aplikace a použijte odkazy pro další informace.

> [!TIP]
> Kterákoli aplikace registrovaná v Azure AD může používat koncový bod platformy Microsoft identity, i když se nepodepisují osobní účty.  Tímto způsobem můžete migrovat stávající aplikace na Microsoft Identity Platform a [MSAL](reference-v2-libraries.md) , aniž byste museli aplikaci znovu vytvářet.

## <a name="tokens"></a>Tokeny

Implementace OAuth 2,0 a OpenID Connect pro Microsoft Identity Platform využívá rozsáhlé použití nosných tokenů, včetně nosných tokenů reprezentovaných jako JWTs (webové tokeny JSON). Nosný token je jednoduchý token zabezpečení, který uděluje přístup k chráněnému prostředku "nosičem". V tomto smyslu je "nosičem" kterákoli strana, která může token prezentovat. I když se strana musí nejdřív ověřit s platformou Microsoft identity, aby získala nosný token, pokud se požadované kroky neberou k zabezpečení tokenu v přenosech a úložištích, může se zachytit a použít neúmyslná strana. I když některé tokeny zabezpečení mají integrovaný mechanismus pro zabránění neoprávněným stranám v jejich použití, tokeny nosiče nemají tento mechanismus a musí se přenášet do zabezpečeného kanálu, jako je protokol HTTPS (Transport Layer Security). Pokud se přenáší nosný token, může se jednat o zneužití útoku prostředníkem k získání tokenu a jeho použití pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí i při ukládání nebo ukládání tokenů nosiče do mezipaměti pro pozdější použití. Vždycky Ujistěte se, že vaše aplikace zabezpečeně přenáší a ukládá tokeny nosiče. Další informace o zabezpečení u nosných tokenů najdete v [části RFC 6750 oddílu 5](https://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typech tokenů použitých na koncovém bodu platformy Microsoft Identity Platform jsou k dispozici v referenčních informacích k [tokenu koncového bodu platformy Microsoft Identity](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připraveni zobrazit některé ukázkové požadavky, začněte s jedním z následujících kurzů. Každé z nich odpovídá konkrétnímu scénáři ověřování. Pokud potřebujete pomáhat s určením, který z nich je pro vás ten správný, podívejte [se na typy aplikací, které můžete vytvořit s platformou Microsoft Identity](v2-app-types.md).

* [Sestavování mobilní a nativní aplikace s OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Sestavování webových aplikací pomocí OpenID Connect](v2-protocols-oidc.md)
* [Sestavování jednostránkovéch aplikací pomocí implicitního toku OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
* [Vytváření procesů démonů nebo procesů na straně serveru s tokem přihlašovacích údajů klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)
* [Získání tokenů ve webovém rozhraní API s využitím toku OAuth 2,0 za běhu](v2-oauth2-on-behalf-of-flow.md)
