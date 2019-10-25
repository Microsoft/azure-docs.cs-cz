---
title: Mobilní aplikace, která volá webová rozhraní API – přehled
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98f05470a07bad82a1e51517a787fb98c78f92ce
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803757"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scénář: mobilní aplikace, která volá webová rozhraní API

Naučte se všechno, co potřebujete vědět, abyste mohli vytvořit mobilní aplikaci, která volá webová rozhraní API.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Vytvořte svou první mobilní aplikaci a vyzkoušejte si rychlý Start!

> [!div class="nextstepaction"]
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro Android](./quickstart-v2-android.md)
>
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro iOS](./quickstart-v2-ios.md)
>
> [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace Xamarin iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Přehled

Pro mobilní aplikace je nezbytné přizpůsobené a bezproblémové uživatelské prostředí.  Platforma Microsoft Identity umožňuje vývojářům v mobilních aplikacích vytvářet prostředí pro uživatele s iOS a Androidem. Vaše aplikace se může přihlašovat Azure Active Directory (Azure AD), uživatelům pro uživatele a Azure AD B2C a získat tokeny pro volání webového rozhraní API účet Microsoft vaším jménem. K implementaci těchto toků použijeme Microsoft Authentication Library (MSAL), který implementuje standardní [tok autorizačního kódu OAuth 2.0](v2-oauth2-auth-code-flow.md).

![Aplikace démonů](./media/scenarios/mobile-app.svg)

Požadavky na mobilní aplikace:

- **Činnost koncového uživatele je klíč**: umožňuje uživatelům zobrazit hodnotu vaší aplikace před vyžádáním přihlášení a požádat pouze o požadovaná oprávnění.
- **Podpora všech uživatelských konfigurací**: mnoho mobilních uživatelů v rámci zásad podmíněného přístupu a dodržování předpisů zařízením. Ujistěte se, že tyto klíčové scénáře podporujete.
- **Implementace jednotného přihlašování (SSO)** : MSAL a Microsoft Identity Platform umožňují jednoduché jednotné přihlašování prostřednictvím prohlížeče zařízení nebo Microsoft Authenticator (a portál společnosti Intune na Androidu).

## <a name="specifics"></a>Specifika

Pamatujte na tyto požadavky při vytváření mobilní aplikace na platformě Microsoft identity:

- V závislosti na platformě může být nutné při prvním přihlášení uživatele vyžadovat určitou interakci s uživatelem. Například iOS vyžaduje, aby aplikace při Microsoft Authenticator prvním použití jednotného přihlašování (a Portál společnosti Intune na Androidu) zobrazily interakci s uživatelem.
- V systémech iOS a Android může MSAL použít externí prohlížeč (který se může zobrazit v horní části vaší aplikace) pro přihlášení uživatelů. Konfiguraci můžete přizpůsobit tak, aby se místo toho používala v webviewech v aplikaci.
- Nikdy nepoužívejte tajný klíč v mobilní aplikaci. Bude k dispozici pro všechny uživatele.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Registrace aplikace](scenario-mobile-app-registration.md)
