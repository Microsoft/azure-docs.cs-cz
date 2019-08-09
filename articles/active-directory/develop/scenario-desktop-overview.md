---
title: Aplikace klasické pracovní plochy, která volá webová rozhraní API (přehled) – Microsoft Identity Platform
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (přehled)
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5288fe57b2f83522b140f65fa62e08f9c63a7af5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852687"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénář: Desktopová aplikace, které volá webová rozhraní API

Naučte se všechno, co potřebujete k vytvoření desktopové aplikace, která volá webová rozhraní API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Pokud jste to ještě neudělali, vytvořte svou první aplikaci pomocí Průvodce rychlým startem pro platformu .NET nebo rychlý Start pro UWP:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volání Microsoft Graph API z desktopové aplikace pro Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volání Microsoft Graph API z aplikace pro UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Přehled

Napíšete desktopovou aplikaci a chcete se přihlašovat do vaší aplikace a volat webová rozhraní API, jako jsou Microsoft Graph, jiná rozhraní Microsoft API nebo vlastní webové rozhraní API. Máte několik možností:

- Můžete použít získání interaktivního tokenu:

  - Pokud vaše desktopová aplikace podporuje grafické ovládací prvky, například pokud se jedná o aplikaci Windows. Form nebo aplikaci WPF.
  - Pokud se jedná o aplikaci .NET Core a souhlasíte s tím, že k interakci s ověřováním v Azure AD dojde v prohlížeči systému

- U hostovaných aplikací pro Windows je taky možné, že aplikace běžící na počítačích, které jsou připojené k doméně Windows nebo AAD, se připojily k tichému získání tokenu pomocí integrovaného ověřování systému Windows.
- A konečně, i když se to nedoporučuje, můžete použít uživatelské jméno/heslo ve veřejných klientských aplikacích. Je stále potřeba v některých scénářích (například DevOps), ale Upozorňujeme, že při použití bude omezení pro vaši aplikaci. Například se nemůže přihlásit uživatel, který potřebuje službu Multi-Factor Authentication (podmíněný přístup). Vaše aplikace navíc nebude využívat jednotné přihlašování (SSO).

  Je také na principech moderního ověřování a je poskytována pouze z původních důvodů.

  ![Desktopová aplikace](media/scenarios/desktop-app.svg)

- Při psaní přenosného nástroje příkazového řádku – pravděpodobně aplikace .NET Core běžící v systému Linux nebo Mac – a pokud souhlasíte s tím, že je ověřování delegované do prohlížeče systému, budete moct používat interaktivní ověřování. (.NET Core zatím neposkytuje [webový prohlížeč](https://aka.ms/msal-net-uses-web-browser) , a proto se ověřování provádí v prohlížeči systému), v opačném případě je nejlepší volbou v tomto případě použití toku kódu zařízení. Tento tok se používá také pro aplikace bez prohlížeče, jako jsou aplikace IoT.

  ![Aplikace s prohlížečem](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifika

Aplikace klasické pracovní plochy mají určitou řadu možností, které závisí hlavně na tom, jestli vaše aplikace používá interaktivní ověřování nebo ne.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace klasické pracovní plochy – registrace aplikace](scenario-desktop-app-registration.md)
