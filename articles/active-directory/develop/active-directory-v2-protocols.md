---
title: Autorizační protokoly podporované platformou Microsoft Identity Platform | Azure
description: Průvodce pro protokoly OAuth 2,0 a OpenID Connect, které podporuje koncový bod Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04ba26f592c02814412493cf4811e30aefa6ee3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918284"
---
# <a name="microsoft-identity-platform-protocols"></a>Protokoly platformy Microsoft Identity Platform

Koncový bod Microsoft Identity Platform pro identity jako službu s oborovými standardními protokoly OpenID Connect a OAuth 2,0. I když je služba kompatibilní se standardy, můžou být mezi dvěma implementacemi těchto protokolů malé rozdíly. Informace zde budou užitečné, pokud se rozhodnete napsat kód přímým odesláním a zpracováním požadavků HTTP nebo použitím open source knihovny od třetí strany, nikoli pomocí některé z našich [Open Source knihoven](reference-v2-libraries.md).

> [!NOTE]
> Ne všechny scénáře a funkce služby Azure AD jsou podporovány koncovým bodem platformy Microsoft Identity Platform. Pokud chcete zjistit, jestli byste měli použít koncový bod platformy Microsoft identity, přečtěte si informace o [omezeních platformy Microsoft Identity](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

U téměř všech toků OAuth 2,0 a OpenID Connect se v systému Exchange účastní čtyři strany:

![Diagram znázorňující role OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Autorizační Server** je koncovým bodem platformy Microsoft Identity Platform a zodpovídá za to, aby uživatel mohl zajistit jeho identitu, udělení a odvolání přístupu k prostředkům a vydávání tokenů. Autorizační Server se také označuje jako poskytovatel identity – bez bezpečného zpracovává cokoli s informacemi uživatele, jejich přístupem a vztahy důvěryhodnosti mezi stranami v toku.
* **Vlastníkem prostředku** je obvykle koncový uživatel. Je to strana, která vlastní data a má oprávnění k tomu, aby třetím stranám umožnila přístup k těmto datům nebo prostředkům.
* **Klient OAuth** je vaše aplikace, identifikovaný identifikátorem jeho aplikace. Klient OAuth je obvykle strana, se kterou koncový uživatel komunikuje, a žádá o tokeny od autorizačního serveru. Klient musí mít udělené oprávnění pro přístup k prostředku vlastníkem prostředku.
* **Server prostředků** je místo, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru za účelem bezpečného ověřování a autorizace klienta OAuth a používá přístupové tokeny nosiče k zajištění toho, aby bylo možné udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikací

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

Kde `{tenant}` může mít jednu ze čtyř různých hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` | Umožňuje uživatelům, aby se do aplikace přihlásili pomocí osobních účtů Microsoft a pracovních/školních účtů z Azure AD. |
| `organizations` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s pracovními nebo školními účty ze služby Azure AD. |
| `consumers` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s osobními účty Microsoft (MSA). |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Umožňuje, aby se do aplikace přihlásili jenom uživatelé s pracovními nebo školními účty z konkrétního tenanta Azure AD. Dá se použít popisný název domény tenanta Azure AD nebo identifikátor GUID klienta. |

Pokud se chcete dozvědět, jak s těmito koncovými body pracovat, vyberte v části [protokoly](#protocols) konkrétní typ aplikace a použijte odkazy pro další informace.

> [!TIP]
> Kterákoli aplikace registrovaná v Azure AD může používat koncový bod platformy Microsoft identity, i když se nepodepisují osobní účty.  Tímto způsobem můžete migrovat stávající aplikace na Microsoft Identity Platform a [MSAL](reference-v2-libraries.md) , aniž byste museli aplikaci znovu vytvářet.  

## <a name="tokens"></a>Tokeny

Implementace OAuth 2,0 a OpenID Connect pro Microsoft Identity Platform využívá rozsáhlou použití nosných tokenů, včetně nosných tokenů reprezentovaných jako JWTs. Nosný token je jednoduchý token zabezpečení, který uděluje přístup k chráněnému prostředku "nosičem". V tomto smyslu je "nosičem" kterákoli strana, která může token prezentovat. I když se strana musí nejdřív ověřit s platformou Microsoft identity, aby získala nosný token, pokud se požadované kroky neberou k zabezpečení tokenu v přenosech a úložištích, může se zachytit a použít neúmyslná strana. I když některé tokeny zabezpečení mají integrovaný mechanismus pro zabránění neoprávněným stranám v jejich použití, tokeny nosiče nemají tento mechanismus a musí se přenášet do zabezpečeného kanálu, jako je protokol HTTPS (Transport Layer Security). Pokud se přenáší nosný token, může se jednat o zneužití útoku prostředníkem k získání tokenu a jeho použití pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí i při ukládání nebo ukládání tokenů nosiče do mezipaměti pro pozdější použití. Vždycky Ujistěte se, že vaše aplikace zabezpečeně přenáší a ukládá tokeny nosiče. Další informace o zabezpečení u nosných tokenů najdete v [části RFC 6750 oddílu 5](https://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typech tokenů použitých na koncovém bodu platformy Microsoft Identity Platform jsou k dispozici v referenčních informacích k [tokenu koncového bodu platformy Microsoft Identity](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připraveni zobrazit některé ukázkové požadavky, začněte s jedním z následujících kurzů. Každé z nich odpovídá konkrétnímu scénáři ověřování. Pokud potřebujete vám určit, který z nich je pro vás ten správný, podívejte [se na typy aplikací, které můžete vytvořit pomocí platformy Microsoft Identity](v2-app-types.md).

* [Sestavování mobilní a nativní aplikace s OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Sestavování webových aplikací pomocí OpenID Connect](v2-protocols-oidc.md)
* [Sestavování jednostránkovéch aplikací pomocí implicitního toku OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
* [Vytváření procesů démonů nebo procesů na straně serveru s tokem přihlašovacích údajů klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)
* [Získání tokenů ve webovém rozhraní API s využitím toku OAuth 2,0 za běhu](v2-oauth2-on-behalf-of-flow.md)
