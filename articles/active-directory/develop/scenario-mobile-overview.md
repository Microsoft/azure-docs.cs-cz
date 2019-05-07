---
title: Mobilní aplikace, která volá webové rozhraní API – přehled | Platforma identit Microsoft
description: Další informace o vytváření mobilních aplikací, že volání webových rozhraní API (přehled)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076492"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: Mobilní aplikace, že volání webových rozhraní API

Přečtěte si všechno, co potřebujete k vytvoření mobilní aplikace, která volá webové rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvoření první mobilní aplikace a vyzkoušejte si rychlý start!

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volat Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
>
> [Rychlé zprovoznění: Získání tokenu a volat Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
>
> [Rychlé zprovoznění: Získání tokenu a volat Microsoft Graph API z Xamarin pro iOS a aplikace pro Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Přehled

Při vytváření mobilních aplikací, je nezbytné prostředí osobní, Bezproblémová koncového uživatele.  Platforma identit Microsoft umožňuje vývojářům mobilních přesně to pro iOS a Android uživatelé provést. Ve službě Azure AD, osobní účet Microsoft, můžete podepsat aplikaci a uživatele Azure AD B2C a získat tokeny pro volání webového rozhraní API za ně. Pokud chcete implementovat tyto toky, použijeme Microsoft Authentication Library (MSAL), který implementuje standard odvětví v oblasti [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Důležité informace o mobilní aplikace:

- ***Činnost koncového uživatele je klíč***: Povolit uživatelům zobrazit hodnotu aplikace před s žádostí o přihlášení a požadovat pouze potřebná oprávnění.
- ***Podpora všech uživatelských konfigurací***: Mnoho mobilních podnikoví uživatelé jsou poskytovány podmíněného přístupu a zásady dodržování předpisů zařízením. Ujistěte se, že podporují tyto klíčové scénáře.
- ***Implementaci jednotného přihlašování (SSO)***: Knihovna MSAL a platforma identit Microsoft zjednodušení povolení jednotného přihlašování přes prohlížeč zařízení nebo aplikace Microsoft Authenticator (a portál společnosti Intune v Androidu).

## <a name="specifics"></a>Specifika

Při vytváření mobilních aplikací na platformě Microsoft identity, začátku do konce prostředí má několik důležitých informací:

- V závislosti na platformě přihlašování bez nutnosti zásahu nemusí být možné při prvním přihlášení. iOS, například vyžaduje aplikací, aby zobrazoval interakci s uživatelem, při získávání jednotného přihlašování k první čas prostřednictvím Microsoft Authenticator (a portál společnosti Intune v Androidu).
- V Iosu a Androidu může MSAL přihlásit uživatele pomocí externího prohlížeče, (které mohou zobrazovat nad vaší aplikace). To lze přizpůsobit, aby místo toho použít v aplikaci Webview.
- Nikdy nepoužívejte tajného kódu v mobilní aplikaci, bude přístupný pro všechny uživatele.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
