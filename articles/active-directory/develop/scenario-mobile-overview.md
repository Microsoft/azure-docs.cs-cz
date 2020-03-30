---
title: Vytvoření mobilní aplikace, která volá webová api | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API (přehled)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6675d67299091325fcc3e12572a906716bf5b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132423"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: Mobilní aplikace, která volá webová api

Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvořte si první mobilní aplikaci a vyzkoušejte rychlý start.

> [!div class="nextstepaction"]
> [Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
>
> [Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
>
> [Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z aplikace Xamarin pro iOS a Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Přehled

Pro mobilní aplikace je nezbytné přizpůsobitelé a bezproblémové uživatelské prostředí.  Platforma identit Microsoftu umožňuje mobilním vývojářům vytvářet toto prostředí pro uživatele iOS a Android. Vaše aplikace se může přihlásit k uživatelům služby Azure Active Directory (Azure AD), osobním uživatelům účtů Microsoft a uživatelům Azure AD B2C. Může také získat tokeny pro volání webového rozhraní API jejich jménem. K implementaci těchto toků použijeme Microsoft Authentication Library (MSAL). MSAL implementuje průmyslový standard [OAuth2.0 tok autorizačního kódu](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Důležité informace o mobilních aplikacích:

- **Uživatelské prostředí je klíčové**: Umožněte uživatelům zobrazit hodnotu vaší aplikace, než požádáte o přihlášení. Požádejte pouze o požadovaná oprávnění.
- **Podpora všech konfigurací uživatelů**: Mnoho mobilních podnikových uživatelů musí dodržovat zásady podmíněného přístupu a zásady dodržování předpisů zařízení. Ujistěte se, že podporujete tyto klíčové scénáře.
- **Implementujte jednotné přihlašování (SSO):** Pomocí platformy MSAL a Microsoft identitmůžete povolit jednotné přihlašování prostřednictvím prohlížeče zařízení nebo Microsoft Authenticator (a Portál společnosti Intune v systému Android).

## <a name="specifics"></a>Specifika

Při vytváření mobilní aplikace na platformě identit Microsoftu mějte na paměti následující důležité informace:

- V závislosti na platformě může být při prvním přihlášení uživatelů vyžadována určitá interakce s uživatelem. Například iOS vyžaduje, aby aplikace zobrazovat interakci uživatele při jejich prvním použití přiřazovat služby zabezpečení poprvé prostřednictvím Microsoft Authenticator (a Intune portál společnosti v systému Android).
- V systémech iOS a Android může služba MSAL k přihlášení uživatelů používat externí prohlížeč. Externí prohlížeč se může zobrazit v horní části aplikace. Místo toho můžete přizpůsobit konfiguraci tak, aby používala webová zobrazení v aplikaci.
- Nikdy nepoužívejte tajný klíč v mobilní aplikaci. V těchto aplikacích jsou tajné klíče přístupné všem uživatelům.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
