---
title: Sestavení desktopové aplikace, která volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (přehled)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ea6ecf456bbcea01bf4c1eef5377d918bf0918fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798948"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénář: desktopová aplikace, která volá webová rozhraní API

Seznamte se s tím, že potřebujete vytvořit desktopovou aplikaci, která volá webová rozhraní API.

## <a name="get-started"></a>Začínáme

Pokud jste to ještě neudělali, vytvořte svou první aplikaci dokončením rychlého startu:

- [Rychlý start: Získání tokenu a volání rozhraní API Microsoft Graph z desktopové aplikace Windows](./quickstart-v2-windows-desktop.md)
- [Rychlý Start: získání tokenu a volání Microsoft Graph API z aplikace pro UWP](./quickstart-v2-uwp.md)
- [Rychlý Start: získání tokenu a volání Microsoft Graph API z nativní aplikace macOS](./quickstart-v2-ios.md)
- [Rychlý Start: získání tokenu a volání Microsoft Graph API z Node.js & elektronovou aplikací](./quickstart-v2-nodejs-desktop.md)

## <a name="overview"></a>Přehled

Napíšete desktopovou aplikaci a chcete se přihlašovat do vaší aplikace a volat webová rozhraní API, jako jsou Microsoft Graph, jiná rozhraní API Microsoftu nebo vlastní webové rozhraní API. Máte několik možností:

- Můžete použít získání interaktivního tokenu:

  - Pokud vaše desktopová aplikace podporuje grafické ovládací prvky, například, pokud se jedná o aplikaci Windows. Form, aplikaci WPF nebo nativní aplikaci macOS.
  - Nebo, pokud se jedná o aplikaci .NET Core a souhlasíte s tím, že je interakce ověřování s Azure Active Directory (Azure AD) provedena v prohlížeči systému.
  - Nebo, pokud se jedná o Node.js elektronickou aplikaci, která běží na instanci Chromu.

- U hostovaných aplikací pro Windows je také možné, že aplikace běžící na počítačích připojených k doméně systému Windows nebo službě Azure AD byly připojeny k tichému získání tokenu pomocí integrovaného ověřování systému Windows.
- A konečně, i když se to nedoporučuje, můžete použít uživatelské jméno a heslo ve veřejných klientských aplikacích. Je stále potřeba v některých scénářích, jako je DevOps. Použití omezení pro vaši aplikaci. Například se nemůže přihlásit uživatel, který potřebuje službu [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (podmíněný přístup). Vaše aplikace navíc nebude využívat jednotné přihlašování (SSO).

  Je také na principech moderního ověřování a je poskytována pouze z původních důvodů.

  ![Desktopová aplikace](media/scenarios/desktop-app.svg)

- Při psaní přenosného nástroje příkazového řádku, pravděpodobně aplikace .NET Core, která je spuštěna v systému Linux nebo Mac, a pokud přijmete Toto ověřování, bude delegováno do prohlížeče systému, můžete použít interaktivní ověřování. .NET Core neposkytuje [webový prohlížeč](https://aka.ms/msal-net-uses-web-browser), takže k ověřování dojde v prohlížeči systému. V opačném případě nejlepší možnost v takovém případě používá tok kódu zařízení. Tento tok se používá také pro aplikace bez prohlížeče, jako jsou aplikace IoT.

  ![Aplikace s prohlížečem](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifika

Aplikace klasické pracovní plochy mají určitou řadu. Závisí hlavně na tom, jestli vaše aplikace používá interaktivní ověřování nebo ne.

## <a name="recommended-reading"></a>Doporučené čtení

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři [Registrace aplikace](scenario-desktop-app-registration.md).
