---
title: Principy ověřování rozhraní API – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit k virtuálním i hraničním určovacím i hraničním určovatelům pomocí digitálních dvojčat Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512994"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Připojení k api a ověření pomocí

Azure Digital Twins používá Azure Active Directory (Azure AD) k ověřování uživatelů a ochraně aplikací. Azure AD podporuje ověřování pro celou řadu moderních architektur. Všechny jsou založeny na standardních protokolech OAuth 2.0 nebo OpenID Connect. Kromě toho vývojáři můžete použít Azure AD k vytváření aplikací pro jednoho klienta a obchodní (LOB). Vývojáři také můžete použít Azure AD k [vývoji víceklientských aplikací](how-to-multitenant-applications.md).

Přehled Azure AD najdete na [stránce základy](https://docs.microsoft.com/azure/active-directory/fundamentals/) pro podrobné průvodce, koncepty a rychlé starty.

> [!TIP]
> Podle [kurzu](tutorial-facilities-setup.md) nastavte a spusťte ukázkovou aplikaci Azure Digital Twins.

K integraci aplikace nebo služby pomocí Azure AD musí vývojář nejdřív aplikaci zaregistrovat v Azure AD. Podrobné pokyny a snímky obrazovky naleznete v [tomto rychlém startu](../active-directory/develop/quickstart-register-app.md).

Azure AD podporuje [pět scénářů primární aplikace:](../active-directory/develop/v2-app-types.md)

* Jednostránková aplikace (SPA): Uživatel se musí přihlásit k jednostránkové aplikaci, která je zabezpečená službou Azure AD.
* Webový prohlížeč na webovou aplikaci: Uživatel se musí přihlásit k webové aplikaci, která je zabezpečená službou Azure AD.
* Nativní aplikace pro webové rozhraní API: Nativní aplikace, která běží na telefonu, tabletu nebo počítači, potřebuje ověřit uživatele, aby získala prostředky z webového rozhraní API, které je zabezpečené službou Azure AD.
* Webová aplikace do webového rozhraní API: Webová aplikace potřebuje získat prostředky z webového rozhraní API zabezpečeného službou Azure AD.
* Daemon nebo serverová aplikace do webového rozhraní API: Daemon aplikace nebo serverové aplikace bez webového uživatelského rozhraní potřebuje získat prostředky z webového rozhraní API zabezpečené ho AD Azure.

> [!IMPORTANT]
> Azure Digital Twins podporuje obě následující ověřovací knihovny:
> * Novější [knihovna ověřování společnosti Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Knihovna ověřování služby Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Volání digitálních dvojčat z webového rozhraní API střední vrstvy

Když vývojáři architekt digitální dvojčata řešení, obvykle vytvořit středně vrstvy aplikace nebo rozhraní API. Aplikace nebo rozhraní API pak volá rozhraní API digitálních dvojčat pod proudem. Chcete-li podporovat tuto standardní architekturu webového řešení, ujistěte se, že uživatelé nejprve:

1. Ověření pomocí aplikace střední vrstvy

1. OAuth 2.0 On-Behalf-Of token je získán během ověřování

1. Získaný token se pak používá k ověření s nebo volání api, které jsou dále po proudu pomocí toku on-behalf-of

Pokyny o tom, jak organizovat on-behalf-of toku, přečtěte si [OAuth 2.0 On-Behalf-Of toku](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). Ukázky kódu můžete také zobrazit v [aplikaci Volání podřízeného webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Další kroky

Chcete-li konfigurovat a testovat azure digitální dvojče pomocí OAuth 2.0 implicitní grant tok, [přečtěte si článek Konfigurace Postman](./how-to-configure-postman.md).

Další informace o zabezpečení Azure Digital Twins najdete v části [Vytvoření a správa přiřazení rolí](./security-create-manage-role-assignments.md).
