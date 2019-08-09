---
title: Mobilní aplikace, která volá webová rozhraní API – přehled | Platforma Microsoft identity
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f67f063ab374dec8bac3905ea46f1dfa9d8ed1fb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852609"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: Mobilní aplikace, která volá webová rozhraní API

Naučte se všechno, co potřebujete vědět, abyste mohli vytvořit mobilní aplikaci, která volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvořte svou první mobilní aplikaci a vyzkoušejte si rychlý Start!

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volání Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
>
> [Rychlé zprovoznění: Získání tokenu a volání Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
>
> [Rychlé zprovoznění: Získání tokenu a volání Microsoft Graph API z aplikace Xamarin iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Přehled

Pro mobilní aplikace je nezbytné přizpůsobené a bezproblémové uživatelské prostředí.  Platforma Microsoft Identity umožňuje vývojářům v mobilních aplikacích vytvářet prostředí pro uživatele s iOS a Androidem. Vaše aplikace se může přihlašovat Azure Active Directory (Azure AD), uživatelům pro uživatele a Azure AD B2C a získat tokeny pro volání webového rozhraní API účet Microsoft vaším jménem. K implementaci těchto toků použijeme Microsoft Authentication Library (MSAL), který implementuje standardní [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Požadavky na mobilní aplikace:

- **Činnost koncového uživatele je klíč**: Umožňuje uživatelům zobrazit hodnotu vaší aplikace před vyžádáním přihlášení a požádat pouze o požadovaná oprávnění.
- **Podpora všech uživatelských konfigurací**: V rámci zásad podmíněného přístupu a dodržování předpisů zařízením je mnoho uživatelů mobilních firem. Ujistěte se, že tyto klíčové scénáře podporujete.
- **Implementace jednotného přihlašování (SSO)** : MSAL a Microsoft Identity Platform umožňují jednoduché jednotné přihlašování prostřednictvím prohlížeče zařízení nebo Microsoft Authenticator (a Portál společnosti Intune na Androidu).

## <a name="specifics"></a>Specifika

Pamatujte na tyto požadavky při vytváření mobilní aplikace na platformě Microsoft identity:

- V závislosti na platformě může být nutné při prvním přihlášení uživatele vyžadovat určitou interakci s uživatelem. Například iOS vyžaduje, aby aplikace při Microsoft Authenticator prvním použití jednotného přihlašování (a Portál společnosti Intune na Androidu) zobrazily interakci s uživatelem.
- V systémech iOS a Android může MSAL použít externí prohlížeč (který se může zobrazit v horní části vaší aplikace) pro přihlášení uživatelů. Konfiguraci můžete přizpůsobit tak, aby se místo toho používala v webviewech v aplikaci.
- Nikdy nepoužívejte tajný klíč v mobilní aplikaci. Bude k dispozici pro všechny uživatele.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
