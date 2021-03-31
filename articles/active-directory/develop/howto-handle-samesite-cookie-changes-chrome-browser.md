---
title: Postup zpracování změn souborů cookie SameSite v prohlížeči Chrome | Azure
titleSuffix: Microsoft identity platform
description: Naučte se zpracovávat změny souborů cookie SameSite v prohlížeči Chrome.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94444907"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Zpracování změn souborů cookie SameSite v prohlížeči Chrome

## <a name="what-is-samesite"></a>Co je SameSite?

`SameSite` je vlastnost, kterou je možné nastavit v souborech cookie protokolu HTTP, aby se zabránilo útokům na CSRF (mezi lokalitami) ve webových aplikacích:

- Když `SameSite` je nastavená na **LAX**, soubor cookie se pošle v žádostech v rámci stejné lokality a v žádosti o získání požadavků z jiných lokalit. Není odesílána v požadavcích GET, které jsou mezi doménami.
- Hodnota **Strict** zajistí, že se soubor cookie pošle v žádostech jenom v rámci stejné lokality.

Ve výchozím nastavení není `SameSite` hodnota nastavena v prohlížečích a proto neexistují žádná omezení odesílaných souborů cookie v požadavcích. Aplikace by musela souhlasit s ochranou CSRF nastavením **LAX** nebo **Strict** na jejich požadavky.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite změny a dopad na ověřování

Nedávné [aktualizace standardů na SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) navrhují ochranu aplikací tím, že se nastaví výchozí chování, `SameSite` když není hodnota nastavená na LAX. Toto zmírnění znamená, že soubory cookie budou omezeny na požadavky HTTP s výjimkou toho, že jsou vytvořeny z jiných lokalit. Kromě toho není k dispozici **žádná hodnota žádného** z důvodů odebrání omezení odesílaných souborů cookie. Tyto aktualizace budou brzy vydány v nadcházející verzi prohlížeče Chrome.

Když se webové aplikace ověřují s platformou Microsoft identity pomocí režimu odpovědi form_post, přihlašovací server odpoví na aplikaci pomocí HTTP POST a pošle tokeny nebo ověřovací kód. Vzhledem k tomu, že se jedná o požadavek mezi doménami (od `login.microsoftonline.com` do vaší domény `https://contoso.com/auth` ), soubory cookie, které byly nastaveny vaší aplikací, teď spadají do nových pravidel v Chrome. Soubory cookie, které je potřeba použít při scénářích mezi lokalitami, jsou soubory cookie, které obsahují hodnoty *stavu* a hodnoty *nonce* , které se také odesílají v žádosti o přihlášení. Služba Azure AD zahodila jiné soubory cookie, aby se relace mohla uchovávat.

Pokud vaše webové aplikace neaktualizujete, výsledkem tohoto nového chování bude selhání ověřování.

## <a name="mitigation-and-samples"></a>Zmírnění a ukázky

Webové aplikace, které se ověřují s platformou Microsoft identity, můžou překonat selhání ověřování nastavením `SameSite` vlastnosti `None` pro soubory cookie, které se používají ve scénářích mezi doménami při spuštění v prohlížeči Chrome.
Další prohlížeče (kompletní seznam najdete [tady](https://www.chromium.org/updates/same-site/incompatible-clients) ) `SameSite` a neobsahují soubory cookie, pokud `SameSite=None` je nastavený.
Proto je nutné, aby se podpora ověřování na více webových aplikacích v prohlížečích nastavila `SameSite` `None` jenom na Chrome a v ostatních prohlížečích ponechali hodnotu prázdnou.

Tento přístup je znázorněn v našich ukázkách kódu níže.

# <a name="net"></a>[.NET](#tab/dotnet)

V následující tabulce jsou uvedeny žádosti o přijetí změn, které pracovaly s SameSitemi změnami v našich ASP.NET a ukázek ASP.NET Core.

| Ukázka | Žádost o přijetí změn |
| ------ | ------------ |
|  [Přírůstkový kurz pro ASP.NET Core webovou aplikaci](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [#261 stejné opravy souborů cookie webu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [Ukázka webové aplikace ASP.NET MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [#35 stejné opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [Active-Directory-dotnet-admin-Restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [#28 stejné opravy souborů cookie webu](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Podrobnosti o tom, jak zpracovávat soubory cookie SameSite v ASP.NET a ASP.NET Core, najdete v tématu také:

- [Práce s SameSite soubory cookie v ASP.NET Core](/aspnet/core/security/samesite) .
- [ASP.NET blog na SameSite problém](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Ukázka |
| ------ |
|  [MS-identity – Python – WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Ukázka | Žádost o přijetí změn |
| ------ | ------------ |
|  [MS-identity – Java – WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [#24 stejné opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [MS-identity – Java – WebApi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [#4 stejné opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Další kroky

Další informace o SameSite a scénáři webové aplikace:

- [Nejčastější dotazy k Google Chrome na SameSite](https://www.chromium.org/updates/same-site/faq)

- [Stránka Chróm SameSite](https://www.chromium.org/updates/same-site)

- [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md)