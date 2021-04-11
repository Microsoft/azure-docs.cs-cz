---
title: Microsoft Identity Platform Authentication Libraries | Azure
description: Seznam klientských knihoven a middlewaru kompatibilního s platformou Microsoft identity Pomocí těchto knihoven můžete přidat podporu přihlašování uživatelů (ověřování) a přístup k chráněnému webovému rozhraní API (autorizace) k vašim aplikacím.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060376"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Knihovny ověřování platformy Microsoft identity

V následujících tabulkách je uvedena podpora knihovny Microsoft Authentication Library pro několik typů aplikací. Obsahují odkazy na zdrojový kód knihovny, kde získat balíček pro projekt vaší aplikace a zda knihovna podporuje přihlášení uživatele (ověřování), přístup k chráněným webovým rozhraním API (autorizaci) nebo obojímu.

Microsoft Identity Platform byl certifikován OpenID Foundation jako [certifikovaný poskytovatel OpenID](https://openid.net/certification/). Pokud upřednostňujete použití jiné knihovny než knihovny Microsoft Authentication Library (MSAL) nebo jiné knihovny podporované společností Microsoft, vyberte jednu s [certifikací s certifikací OpenID Connect](https://openid.net/developers/certified/).

Pokud se rozhodnete ručně nastavit vlastní implementaci [OAuth 2,0 nebo OpenID 1,0 Connect](active-directory-v2-protocols.md)na úrovni protokolu, věnujte velkou pozornost faktorům zabezpečení v každé specifikaci Standard a použijte metodologii pro SDL (Software Development Lifecycle), jako je například [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Jednostránkové aplikace (SPA)

Jednostránkové aplikace běží zcela v prohlížeči a načítá data stránek (HTML, CSS a JavaScript) dynamicky nebo v době načítání aplikace. Může volat webová rozhraní API pro interakci s back-end zdroji dat.

Vzhledem k tomu, že se kód SPA spouští zcela v prohlížeči, považuje se za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Webová aplikace

Webová aplikace spouští kód na serveru, který generuje a odesílá HTML, CSS a JavaScript do webového prohlížeče uživatele, aby jej bylo možné vykreslit. Identita uživatele je udržována jako relace mezi prohlížečem uživatele (front-end) a webovým serverem (back-end).

Vzhledem k tomu, že kód webové aplikace běží na webovém serveru, je považován za *důvěrného klienta* , který dokáže bezpečně ukládat tajné klíče.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Desktopová aplikace

Aplikace klasické pracovní plochy je obvykle binární (zkompilovaný) kód, který zobrazuje uživatelské rozhraní a je určen ke spuštění na ploše uživatele.

Vzhledem k tomu, že se desktopová aplikace spouští na ploše uživatele, považuje se za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Mobilní aplikace

Mobilní aplikace je obvykle binární (zkompilovaný) kód, který zobrazuje uživatelské rozhraní a je určen ke spuštění na mobilním zařízení uživatele.

Vzhledem k tomu, že mobilní aplikace běží na mobilním zařízení uživatele, je považována za *veřejného klienta* , který nedokáže bezpečně ukládat tajné klíče.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Služba/démon

Služby a procesy démony se běžně používají pro komunikaci typu Server-Server a jinou bezobslužnou ( *někdy označovanou jako bezobslužné*). Vzhledem k tomu, že na klávesnici není žádný uživatel, aby bylo možné zadat přihlašovací údaje nebo souhlas s přístupem k prostředkům, tyto aplikace se při žádosti o autorizovaný přístup k prostředkům webového rozhraní API ověřují jako samy sebe, nikoli jako uživatel.

Služba nebo démon, který běží na serveru, se považuje za *důvěrného klienta* , který může své tajné údaje ukládat bezpečně.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Další kroky

Další informace o knihovně ověřování společnosti Microsoft najdete v tématu [Přehled knihovny Microsoft Authentication Library (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
