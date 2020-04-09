---
title: Jak zacházet se změnami souborů cookie SameSite v prohlížeči Chrome | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak v prohlížeči Chrome zacházet se změnami souborů cookie SameSite.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: e414e5cb7ad9097eb815240f83d9f529f839b6b4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883997"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Zpracování změn souborů cookie SameSite v prohlížeči Chrome

## <a name="what-is-samesite"></a>Co je SameSite?

`SameSite`je vlastnost, kterou lze nastavit v souborech cookie HTTP, aby se zabránilo útokům na křížové weby (CSRF) ve webových aplikacích:

- Když `SameSite` je nastavena na **Lax**, cookie je odeslána v žádostech v rámci stejného webu a get požadavky z jiných stránek. Není odeslána v požadavcích GET, které jsou mezi doménami.
- Hodnota **Strict** zajišťuje, že soubor cookie je odeslán v žádostech pouze v rámci stejného webu.

Ve výchozím `SameSite` nastavení není hodnota nastavena v prohlížečích, a proto neexistují žádná omezení pro soubory cookie odesílané v žádostech. Aplikace by se musela přihlásit k ochraně CSRF nastavením **laxní** nebo **přísné** podle jejich požadavků.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite změny a dopad na ověřování

Nedávné [aktualizace standardů na SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) navrhnout ochranu `SameSite` aplikací tím, že výchozí chování, když žádná hodnota je nastavena na Lax. Toto zmírnění znamená, že soubory cookie budou omezeny na požadavky HTTP s výjimkou GET z jiných webů. Kromě toho je zavedena hodnota **None,** která odstraňuje omezení odesílaných souborů cookie. Tyto aktualizace budou brzy vydány v nadcházející verzi prohlížeče Chrome.

Při ověřování webových aplikací pomocí platformy Microsoft Identity pomocí režimu odezvy "form_post", přihlašovací server reaguje na aplikaci pomocí HTTP POST k odeslání tokenů nebo kód ověření. Vzhledem k tomu, že tento `login.microsoftonline.com` požadavek je `https://contoso.com/auth`požadavek napříč doménami (například z vaší domény), soubory cookie, které byly nastaveny vaší aplikací, nyní spadají pod nová pravidla v Chromu. Soubory cookie, které je třeba použít ve scénářích pro více webů, jsou soubory cookie, které uchovávají hodnoty *stavu* a *nonce,* které jsou také odeslány v žádosti o přihlášení. Existují další soubory cookie vynechány Azure AD pro pořádání relace.

Pokud neaktualizujete webové aplikace, toto nové chování bude mít za následek selhání ověřování.

## <a name="mitigation-and-samples"></a>Zmírnění a vzorky

Chcete-li překonat selhání ověřování, webové aplikace ověřování s platformou `SameSite` identit `None` microsoftu můžete nastavit vlastnost pro soubory cookie, které se používají ve scénářích mezi doménami při spuštění v prohlížeči Chrome.
Ostatní prohlížeče (viz [zde](https://www.chromium.org/updates/same-site/incompatible-clients) úplný seznam) sledují `SameSite` předchozí chování a nebudou `SameSite=None` obsahovat soubory cookie, pokud jsou nastaveny.
To je důvod, proč pro podporu ověřování na více `SameSite` prohlížečích `None` webové aplikace budou muset nastavit hodnotu pouze v prohlížeči Chrome a ponechat hodnotu prázdnou v jiných prohlížečích.

Tento přístup je demonstrován v ukázkách kódu níže.

# <a name="net"></a>[.NET](#tab/dotnet)

V následující tabulce jsou uvedeny žádosti o přijetí změn, které fungovaly kolem změny stejného webu v našich ASP.NET a ASP.NET základní ukázky.

| Ukázka | Žádost o přijetí změn |
| ------ | ------------ |
|  [ASP.NET základní webová aplikace přírůstkové kurz](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Stejné soubory cookie webu opravit #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET ukázka webové aplikace MVC](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Stejný #35 opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Stejné #28 opravy souborů cookie webu](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

podrobnosti o tom, jak zacházet se soubory cookie SameSite v ASP.NET a ASP.NET Core, naleznete také:

- [Pracujte se soubory cookie SameSite v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/samesite) .
- [ASP.NET Blog na SameSite vydání](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Ukázka |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Ukázka | Žádost o přijetí změn |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Stejné #24 opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Stejné #4 opravy souborů cookie webu](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Další kroky

Další informace o samesite a scénář webové aplikace:

> [!div class="nextstepaction"]
> [Nejčastější dotazy prohlížeče Google Chrome na webu SameSite](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Stránka Chromium SameSite](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scénář: Webová aplikace, která se připisuje k uživatelům](scenario-web-app-sign-user-overview.md)