---
title: Další informace o protokoly autorizace podporované službou Azure AD v2.0 | Microsoft Docs
description: Průvodce protokolů podporovaných koncového bodu v2.0 Azure AD.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2018
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 29d9e2d9ee05b755ef40179e0e75fb0c8a6b010b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoly - OAuth 2.0 & OpenID Connect
Koncový bod v2.0, můžete použít Azure AD pro identity jako služba pomocí standardních protokolů, OpenID Connect a OAuth 2.0.  Služba je kompatibilní se standardy, může být drobné rozdíly mezi jakékoli dvě implementace těchto protokolů.  Zde uvedené informace budou užitečné, pokud zvolíte možnost zápisu kódu přímo zasláním & zpracování HTTP požadavků nebo použít knihovnu 3. stran s otevřeným zdrojem, nikoli pomocí jednoho z našich [otevřete zdroje knihovny](active-directory-v2-libraries.md).

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0.  Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

## <a name="the-basics"></a>Základní informace
V téměř všechny toky OAuth a OpenID Connect existují čtyři strany účastnící se exchange:

![Role OAuth 2.0](../../media/active-directory-v2-flows/protocols_roles.png)

* **Serveru ověřování** je koncovým bodem v2.0.  Je zodpovědná za zajištění identitu uživatele, udělení a odvolání přístupu k prostředkům a vystavování tokenů.  Je také označován jako zprostředkovatele identity – bezpečně zpracovává nic dělat s informací o uživateli, jejich přístup a vztahy důvěryhodnosti mezi stranami v tok.
* **Vlastník prostředku** je obvykle koncový uživatel.  Je stranu, která je vlastníkem dat a má možnost povolit třetím stranám přistupovat k datům, nebo na prostředek.
* **Klienta OAuth** je aplikaci identifikovanou pomocí jeho ID aplikace.  Je obvykle stranu, která koncový uživatel komunikuje se službou a požaduje tokeny ze serveru ověřování.  Klient musí udělit oprávnění pro přístup k prostředku vlastníka prostředku.
* **Server prostředků** je, kde se nachází prostředků nebo data.  Vztahy důvěryhodnosti serveru ověřování k bezpečně ověřování a autorizaci klientů OAuth a používá access_tokens nosiče k zajištění, že lze udělit přístup k prostředku.

## <a name="app-registration"></a>Registrace aplikací
Každá aplikace používající koncového bodu v2.0 potřeba registrovat u [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) předtím, než může komunikovat pomocí účtu OAuth nebo OpenID Connect.  Proces registrace aplikace bude shromažďovat & přiřadit aplikaci několik hodnot:

* **Id aplikace** jednoznačně identifikuje vaši aplikaci
* A **identifikátor URI pro přesměrování** nebo **identifikátor balíčku** který lze použít k cílení odpovědí zpět do aplikace
* Pár dalších hodnot konkrétní scénáře.

Pro další informace si přečtěte, jak [zaregistrovat aplikaci](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Koncové body
Po registraci aplikace komunikuje se službou Azure AD pomocí zasílání požadavků do koncového bodu v2.0:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Kde `{tenant}` může trvat jednu ze čtyř různých hodnot:

| Hodnota | Popis |
| --- | --- |
| `common` |Umožňuje uživatelům s osobní účty Microsoft i pracovní nebo školní účty ze služby Azure Active Directory pro přihlášení do aplikace. |
| `organizations` |Umožňuje pouze uživatelé s pracovní nebo školní účty ze služby Azure Active Directory pro přihlášení do aplikace. |
| `consumers` |Umožňuje pouze uživatelé s osobní účty Microsoft (MSA) pro přihlášení do aplikace. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` nebo `contoso.onmicrosoft.com` |Povolí pouze uživatelé s pracovní nebo školní účty z konkrétní klienta Azure Active Directory pro přihlášení do aplikace.  Můžete použít buď domény popisný název klienta Azure AD, nebo identifikátor guid klienta. |

Další informace o tom, jak pracovat s těmito koncovými body zvolte jeden z následujících typů konkrétní aplikace.

## <a name="tokens"></a>Tokeny
Implementace v2.0 OAuth 2.0 a OpenID Connect využívají rozsáhlé nosné tokeny, včetně nosné tokeny vyjádřené tokeny Jwt. Je token nosiče tokenu lightweight zabezpečení, který uděluje přístup "nosiče" k chráněnému prostředku. V tomto smyslu je "nosiče" libovolné strany, který může být token. I když může strana musí nejprve ověřit pomocí Azure AD pro příjem token nosiče, nebudou přijata požadované kroky k zabezpečení token v přenos a ukládání, může být zachyceny a použity nezamýšleným strana. I když některé tokeny zabezpečení má integrovanou mechanismus, který brání neoprávněným stranám jejich používání, nosné tokeny nemají tento mechanismus a musí být přenosu v zabezpečený kanál, jako je například transport layer security (HTTPS). Pokud token nosiče je přenesen v nešifrované podobě, man-in střední útok lze škodlivý stranou získat token a používat ho pro neoprávněný přístup k chráněnému prostředku. Při ukládání nebo ukládání do mezipaměti nosné tokeny pro pozdější použití platí stejné zásady zabezpečení. Vždy zajistěte, aby vaše aplikace odesílá a ukládá nosné tokeny zabezpečeným způsobem. Další aspekty zabezpečení na nosné tokeny, najdete v části [RFC 6750 část 5](http://tools.ietf.org/html/rfc6750).

Další podrobnosti o různých typů tokeny, které jsou používány koncového bodu v2.0 je k dispozici v [odkaz tokenu koncový bod v2.0](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokoly
Pokud jste připraveni zobrazíte některých požadavků příklad, začít pracovat s jedním z následující kurzy.  Každé z nich odpovídá konkrétní ověřovacím scénáři.  Pokud potřebujete pomoc při rozhodování, což je správné toku za vás, podívejte se na [s typy aplikací můžete vytvořit pomocí v2.0](active-directory-v2-flows.md).

* [Vytvoření mobilní a nativní aplikace s OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
* [Vytvoření webové aplikace s Open ID Connect](active-directory-v2-protocols-oidc.md)
* [Sestavení jednostránkové aplikace pomocí implicitního toku OAuth 2.0](active-directory-v2-protocols-implicit.md)
* [Sestavení démoni nebo serverové straně procesy pomocí přihlašovacích údajů klienta OAuth 2.0 toku](active-directory-v2-protocols-oauth-client-creds.md)
* [Získat tokeny v rozhraní Web API s tok On jménem Of OAuth 2.0](active-directory-v2-protocols-oauth-on-behalf-of.md)
