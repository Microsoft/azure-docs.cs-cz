---
title: Vytvoření mobilní aplikace, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (přehled).
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132423"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: mobilní aplikace, která volá webová rozhraní API

Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvořte svou první mobilní aplikaci a vyzkoušejte si rychlý Start.

> [!div class="nextstepaction"]
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
>
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
>
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace Xamarin iOS a Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Přehled

Pro mobilní aplikace je nezbytné přizpůsobené a bezproblémové uživatelské prostředí.  Platforma Microsoft Identity umožňuje vývojářům v mobilních aplikacích vytvářet prostředí pro uživatele s iOS a Androidem. Vaše aplikace se může přihlašovat Azure Active Directory (Azure AD), uživatelům osobních účet Microsoft a Azure AD B2Cm uživatelům. Může také získat tokeny pro volání webového rozhraní API jménem. K implementaci těchto toků použijeme Microsoft Authentication Library (MSAL). MSAL implementuje oborový standardní [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Požadavky na mobilní aplikace:

- **Činnost koncového uživatele je klíč**: umožňuje uživatelům zobrazit hodnotu vaší aplikace před tím, než se požádá o přihlášení. Vyžádá pouze požadovaná oprávnění.
- **Podpora všech uživatelských konfigurací**: mnoho uživatelů v mobilních firmách musí splňovat zásady podmíněného přístupu a zásady dodržování předpisů zařízením. Ujistěte se, že tyto klíčové scénáře podporujete.
- **Implementace jednotného přihlašování (SSO)** : pomocí MSAL a platformy Microsoft Identity Platform můžete povolit jednotné přihlašování prostřednictvím prohlížeče nebo Microsoft Authenticator zařízení (a portál společnosti Intune na Androidu).

## <a name="specifics"></a>Specifika

Při vytváření mobilní aplikace na platformě Microsoft identity je potřeba vzít v úvahu následující skutečnosti:

- V závislosti na platformě může být při prvním přihlášení uživatelů vyžadovat určitou interakci s uživatelem. Například iOS vyžaduje, aby aplikace zobrazovaly interakci uživatele při prvním použití jednotného přihlašování prostřednictvím Microsoft Authenticator (a Portál společnosti Intune na Androidu).
- V systémech iOS a Android může MSAL použít externí prohlížeč pro přihlášení uživatelů. Externí prohlížeč se může zobrazit v horní části aplikace. Konfiguraci můžete přizpůsobit tak, aby se místo toho používala v webviewech v aplikaci.
- Nikdy nepoužívejte tajný klíč v mobilní aplikaci. V těchto aplikacích jsou tajné klíče přístupné všem uživatelům.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
