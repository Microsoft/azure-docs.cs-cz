---
title: Aplikace klasické pracovní plochy, že volání webových rozhraní API (přehled) – platforma identit Microsoft
description: Zjistěte, jak vytvářet aplikace klasické pracovní plochy, že volání webových rozhraní API (přehled)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97b32fff931c1c0f5e19a269afec5094cae3d40e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111209"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénář: Desktopová aplikace, které volá webová rozhraní API

Zjistěte všechno, co potřebujete k vytváření desktopových aplikací, která volá webové rozhraní API

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Začínáme

Pokud jste tak dosud neučinili, vytvořte svoji první aplikaci pomocí následujících klasické pracovní plochy tohoto rychlého startu .NET nebo UWP rychlý start:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volat Microsoft Graph API z desktopové aplikace Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Získání tokenu a volat Microsoft Graph API z aplikace pro UPW](./quickstart-v2-uwp.md)

## <a name="overview"></a>Přehled

Psaní aplikací pro stolní počítače a chcete k přihlášení uživatelů do vaší aplikace a zavolání webového rozhraní API, jako je například Microsoft Graphu, jiné APIs Microsoftu nebo vlastní webové rozhraní API. Máte několik možností:

- Pokud aplikace klasické pracovní plochy podporuje grafické prvky, například pokud je Windows.Form aplikace nebo aplikace WPF, můžete použít interaktivní získání tokenu.
- Pro aplikace Windows hostované je také možné pro aplikace běžící na počítačích připojených k doméně Windows nebo k získání tokenu tiše pomocí integrovaného ověřování Windows připojená k AAD.
- A konečně a i když to nedoporučujeme, můžete použít uživatelské jméno/heslo aplikace veřejným klientem. Stále se vyžaduje v některých případech (například DevOps), ale mějte na paměti, že ho pomocí bude ukládat omezení u vaší aplikace. Například se nemůže přihlásit uživatele, který musí projít ověřováním Multi-Factor Authentication (podmíněný přístup). Také vaše aplikace nebude využívat jednotné přihlašování (SSO).

  Také je v rozporu se zásadami moderní ověřování a je k dispozici pouze kvůli starším verzím.

  ![Aplikace klasické pracovní plochy](media/scenarios/desktop-app.svg)

- Pokud píšete přenosné nástroje příkazového řádku – pravděpodobně aplikaci .NET Core běží na systému Linux nebo Mac - nebudete moct používat ani interaktivní ověřování (protože neposkytuje .NET Core [webový prohlížeč](https://aka.ms/msal-net-uses-web-browser)), ani integrovaný režim Ověřování Windows. Nejlepší možností je v takovém případě použijte tok kódu zařízení. Tento tok se také používá pro aplikace bez prohlížeče, jako jsou třeba aplikace iOT

  ![Browserless aplikace](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifika

Desktopové aplikace mít řadu zvláštnosti, což závisí hlavně na tom, jestli vaše aplikace používá interaktivní ověřování nebo ne.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace klasické pracovní plochy – registrace aplikace](scenario-desktop-app-registration.md)
