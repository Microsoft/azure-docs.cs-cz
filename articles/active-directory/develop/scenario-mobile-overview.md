---
title: Vytvoření mobilní aplikace, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform | Azure
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (přehled).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 853ab82ab40cc7ebee1c74feaf6aad87adf39ece
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443105"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: mobilní aplikace, která volá webová rozhraní API

Naučte se, jak vytvořit mobilní aplikaci, která volá webová rozhraní API.

## <a name="getting-started"></a>Začínáme

Pokud jste to ještě neudělali, vytvořte svou první aplikaci dokončením rychlého startu:

- [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
- [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
- [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace Xamarin iOS a Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Přehled

Pro mobilní aplikace je nezbytné přizpůsobené a bezproblémové uživatelské prostředí.  Platforma Microsoft Identity umožňuje vývojářům v mobilních aplikacích vytvářet prostředí pro uživatele s iOS a Androidem. Vaše aplikace se může přihlašovat Azure Active Directory (Azure AD), uživatelům osobních účet Microsoft a Azure AD B2Cm uživatelům. Může také získat tokeny pro volání webového rozhraní API jménem. K implementaci těchto toků použijeme Microsoft Authentication Library (MSAL). MSAL implementuje oborový standardní [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Požadavky na mobilní aplikace:

- **Činnost koncového uživatele je klíč** : umožňuje uživatelům zobrazit hodnotu vaší aplikace před tím, než se požádá o přihlášení. Vyžádá pouze požadovaná oprávnění.
- **Podpora všech uživatelských konfigurací** : mnoho uživatelů v mobilních firmách musí splňovat zásady podmíněného přístupu a zásady dodržování předpisů zařízením. Ujistěte se, že tyto klíčové scénáře podporujete.
- **Implementace jednotného přihlašování (SSO)** : pomocí MSAL a platformy Microsoft Identity Platform můžete povolit jednotné přihlašování prostřednictvím prohlížeče nebo Microsoft Authenticator zařízení (a portál společnosti Intune na Androidu).
- **Implementovat režim sdíleného zařízení** : umožňuje, aby se vaše aplikace používala ve scénářích se sdíleným zařízením, například nemocnicích, výrobou, maloobchodním a finančním. [Přečtěte si další informace o podpoře režimu sdíleného zařízení](msal-shared-devices.md).

## <a name="specifics"></a>Specifika

Při vytváření mobilní aplikace na platformě Microsoft identity je potřeba vzít v úvahu následující skutečnosti:

- V závislosti na platformě může být při prvním přihlášení uživatelů vyžadovat určitou interakci s uživatelem. Například iOS vyžaduje, aby aplikace zobrazovaly interakci uživatele při prvním použití jednotného přihlašování prostřednictvím Microsoft Authenticator (a Portál společnosti Intune na Androidu).
- V systémech iOS a Android může MSAL použít externí prohlížeč pro přihlášení uživatelů. Externí prohlížeč se může zobrazit v horní části aplikace.
- Nikdy nepoužívejte tajný klíč v mobilní aplikaci. V těchto aplikacích jsou tajné klíče přístupné všem uživatelům.

## <a name="recommended-reading"></a>Doporučené čtení

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři [Registrace aplikace](scenario-mobile-app-registration.md).
