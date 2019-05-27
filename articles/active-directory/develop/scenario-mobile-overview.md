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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: df5b53ccdcd33e2542834769baff5b759ff7b170
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962362"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: Mobilní aplikace, že volání webových rozhraní API

Přečtěte si všechno, co potřebujete vědět k vytváření mobilních aplikací, která volá webové rozhraní API.

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

Individuální, propojené uživatelské prostředí je zásadní pro mobilní aplikace.  Platforma identit Microsoft umožňuje vývojáře mobilních aplikací k vytvoření tohoto prostředí pro uživatelé Iosu a Androidu. Vaše aplikace může přihlášení uživatelů Azure Active Directory (Azure AD), osobní uživatele účtu Microsoft a uživatele Azure AD B2C a získat tokeny pro volání webového rozhraní API za ně. Pokud chcete implementovat tyto toky, použijeme Microsoft Authentication Library (MSAL), který implementuje standard odvětví v oblasti [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Důležité informace pro mobilní aplikace:

- **Činnost koncového uživatele je klíč**: Povolit uživatelům zobrazit hodnotu aplikace před s žádostí o přihlášení a vyžádat pouze požadovaná oprávnění.
- **Podpora všech uživatelských konfigurací**: Mnoho mobilních podnikoví uživatelé jsou poskytovány podmíněného přístupu a zásady dodržování předpisů zařízením. Ujistěte se, že podporují tyto klíčové scénáře.
- **Implementaci jednotného přihlašování (SSO)**: Knihovna MSAL a platforma identit Microsoft zjednodušení povolení jednotného přihlašování přes prohlížeč zařízení nebo aplikace Microsoft Authenticator (a portál společnosti Intune v Androidu).

## <a name="specifics"></a>Specifika

Mějte tyto aspekty při vytváření mobilních aplikací na platformě Microsoft identity:

- V závislosti na platformě některé interakci uživatele může být potřeba při prvním přihlášení uživatele. Například iOS vyžaduje, aby zobrazení interakce s uživatelem, při použití jednotného přihlašování při prvním průchodu aplikaci Microsoft Authenticator (a portál společnosti Intune v Androidu) aplikace.
- V Iosu a Androidu může MSAL přihlásit uživatele pomocí externího prohlížeče (která může zobrazovat nad vaší aplikace). Můžete přizpůsobit konfiguraci místo toho použít v aplikaci Webview.
- Nikdy nepoužívejte tajného kódu v mobilní aplikaci. Bude přístupný pro všechny uživatele.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
