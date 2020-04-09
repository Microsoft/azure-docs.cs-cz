---
title: Protokoly OAuth 2.0 a OpenID Connect - platforma identit Microsoft | Azure
description: Průvodce protokoly OAuth 2.0 a OpenID Connect, které jsou podporovány koncovým bodem platformy identit microsoftu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4847bddcbcfbc27502965efa221a3707fa453457
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885663"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protokoly OAuth 2.0 a OpenID Connect na platformě identit Microsoftu

Koncový bod platformy identit microsoftu pro identity-as-a-service s oborovými standardními protokoly, OpenID Connect a OAuth 2.0. Zatímco služba je kompatibilní se standardy, může být jemné rozdíly mezi jakékoli dvě implementace těchto protokolů. Informace zde budou užitečné, pokud se rozhodnete napsat kód přímým odesíláním a zpracováním požadavků HTTP nebo použít knihovnu s otevřeným zdrojovým kódem třetí strany, nikoli pomocí jedné z našich [open-source knihoven](reference-v2-libraries.md).

> [!NOTE]
> Ne všechny scénáře a funkce Azure AD jsou podporovány koncovým bodem platformy identit y Microsoft. Chcete-li zjistit, zda byste měli používat koncový bod platformy identit společnosti Microsoft, přečtěte si o [omezení platformy identit společnosti Microsoft](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Základy

Téměř ve všech tocích OAuth 2.0 a OpenID Connect jsou do výměny zapojeny čtyři strany:

![Diagram znázorňující role OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Autorizační server** je koncový bod platformy identit y společnosti Microsoft a je zodpovědný za zajištění identity uživatele, udělení a zrušení přístupu k prostředkům a vydávání tokenů. Autorizační server také známý jako zprostředkovatel identity – bezpečně zpracovává cokoli, co má co do činění s informacemi o uživateli, jejich přístupem a vztahy důvěryhodnosti mezi stranami v toku.
* **Vlastník prostředku** je obvykle koncový uživatel. Je to strana, která vlastní data a má pravomoc umožnit třetím stranám přístup k těmto datům nebo prostředkům.
* **Klient OAuth** je vaše aplikace, identifikovaná id aplikace. Klient OAuth je obvykle stranou, se kterou koncový uživatel komunikuje, a požaduje tokeny z autorizačního serveru. Klientovi musí být uděleno oprávnění k přístupu k prostředku vlastníkem prostředku.
* **Server prostředků** je místo, kde se nachází prostředek nebo data. Důvěřuje autorizačnímu serveru k bezpečnému ověření a autorizaci klienta OAuth a používá přístupové tokeny nosiče k zajištění udělení přístupu k prostředku.

## <a name="app-registration"></a>Registrace aplikací

Každá aplikace, která chce přijmout osobní i pracovní nebo školní účty, musí být zaregistrovaná prostřednictvím prostředí **registrace aplikací** na webu [Azure Portal,](https://aka.ms/appregistrations) než bude moci tyto uživatele podepsat pomocí OAuth 2.0 nebo OpenID Connect. Proces registrace aplikace bude shromažďovat a přiřazovat několik hodnot do vaší aplikace:

* **ID aplikace,** které jednoznačně identifikuje vaši aplikaci
* Identifikátor **URI přesměrování** (volitelné), který lze použít k přímému vrácení odpovědí do aplikace
* Několik dalších hodnot specifických pro scénář.

Pro další informace si přečtěte, jak [zaregistrovat aplikaci](quickstart-register-app.md).

## <a name="endpoints"></a>Koncové body

Jakmile se aplikace zaregistruje, komunikuje s platformou identit Microsoftu odesláním požadavků do koncového bodu:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Kde `{tenant}` může trvat jednu ze čtyř různých hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` | Umožňuje uživatelům s osobními účty Microsoft a pracovními/školními účty z Azure AD přihlásit se k aplikaci. |
| `organizations` | Umožňuje pouze uživatelům s pracovními nebo školními účty z Azure AD přihlásit se do aplikace. |
| `consumers` | Umožňuje přihlásit se do aplikace pouze uživatelům s osobními účty Microsoft (MSA). |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` | Umožňuje pouze uživatelům s pracovními nebo školními účty z konkrétního klienta Azure AD přihlásit se do aplikace. Lze použít popisný název domény klienta Azure AD nebo identifikátor GUID klienta. |

Pokud se chcete dozvědět, jak s těmito koncovými body pracovat, vyberte konkrétní typ aplikace v části [Protokoly](#protocols) a další informace najdete na odkazech.

> [!TIP]
> Každá aplikace registrovaná ve službě Azure AD může používat koncový bod platformy identit y Microsoft, i když se nepřihlašují k osobním účtům.  Tímto způsobem můžete migrovat existující aplikace na platformu identit microsoftu a [MSAL](reference-v2-libraries.md) bez opětovného vytvoření aplikace.  

## <a name="tokens"></a>Tokeny

Implementace platformy identit Microsoftu OAuth 2.0 a OpenID Connect rozsáhle využívá žetony nosičů, včetně nosných tokenů reprezentovaných jako JWTs. Nosný token je lehký token zabezpečení, který uděluje "nosič" přístup k chráněnému prostředku. V tomto smyslu je "nosič" jakákoli strana, která může prezentovat token. I když strana musí nejprve ověřit pomocí platformy identity společnosti Microsoft přijímat token nosiče, pokud nejsou podniknuty požadované kroky k zabezpečení tokenu v přenosu a úložišti, může být zachycen a používán nezamýšlenou stranou. Zatímco některé tokeny zabezpečení mají vestavěný mechanismus, který brání neoprávněným stranám v jejich použití, tokeny nosiče nemají tento mechanismus a musí být přepravovány v zabezpečeném kanálu, jako je například zabezpečení transportní vrstvy (HTTPS). Pokud je token nosiče přenášen v jasné, škodlivá strana může použít útok man-in-the-middle k získání tokenu a použít jej pro neoprávněný přístup k chráněnému prostředku. Stejné zásady zabezpečení platí při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití. Vždy se ujistěte, že vaše aplikace přenáší a ukládá žetony na doručitele bezpečným způsobem. Další důležité informace o zabezpečení na nosné tokeny, viz [RFC 6750 oddíl 5](https://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typech tokenů používaných v koncovém bodě platformy identity Microsoft u něj jsou k dispozici v [odkazu tokenu tokenu platformy identity Microsoftu](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokoly

Pokud jste připraveni zobrazit některé příklady požadavků, můžete začít s jedním z níže uvedených kurzů. Každý z nich odpovídá konkrétní scénář ověřování. Pokud potřebujete pomoc s určením, který tok je pro vás ten správný, podívejte se [na typy aplikací, které můžete vytvářet pomocí platformy identit Microsoftu](v2-app-types.md).

* [Vytváření mobilních a nativních aplikací s OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Vytváření webových aplikací pomocí OpenID Connect](v2-protocols-oidc.md)
* [Vytváření jednostránkových aplikací s implicitním tokem OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Vytváření daemonů nebo procesů na straně serveru s tokem pověření klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Získejte tokeny ve webovém rozhraní API s OAuth 2.0 jménem Flow](v2-oauth2-on-behalf-of-flow.md)
