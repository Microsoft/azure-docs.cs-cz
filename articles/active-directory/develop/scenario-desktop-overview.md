---
title: Vytvoření aplikace klasické pracovní plochy, která volá webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit desktopovou aplikaci, která volá webová API (přehled)
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
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702143"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scénář: Desktopová aplikace, která volá webová api

Naučte se vše, co potřebujete k vytvoření desktopové aplikace, která volá webová API.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Začínáme

Pokud jste tak dosud neučinili, vytvořte první aplikaci podle rychlého startu na ploše .NET, na úvodním panelu univerzální platformy Windows (UPW) nebo nanativním úvodním startu nativní aplikace macOS:

> [!div class="nextstepaction"]
> [Rychlý start: Získání tokenu a volání rozhraní API Microsoft Graph z desktopové aplikace Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z aplikace UPW](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Úvodní příručka: Získání tokenu a volání rozhraní Microsoft Graph API z nativní aplikace pro macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Přehled

Napíšete desktopovou aplikaci a chcete se přihlásit k vaší aplikaci a volat webová rozhraní API, jako je Microsoft Graph, jiná rozhraní API společnosti Microsoft nebo vlastní webové rozhraní API. Máte několik možností:

- Můžete použít interaktivní pořízení tokenu:

  - Pokud vaše desktopová aplikace podporuje grafické ovládací prvky, například pokud se jedná o aplikaci Windows.Form, aplikaci WPF nebo nativní aplikaci macOS.
  - Nebo pokud se jedná o aplikaci .NET Core a souhlasíte s tím, že k interakci ověřování se službou Azure Active Directory (Azure AD) dojde v systémovém prohlížeči.

- U aplikací hostovaných v systému Windows je také možné, že aplikace spuštěné v počítačích spojených s doménou Windows nebo Azure AD se připojily k tichému získání tokenu pomocí integrovaného ověřování systému Windows.
- Nakonec, a i když to není doporučeno, můžete použít uživatelské jméno a heslo ve veřejných klientských aplikacích. Je to stále potřeba v některých scénářích, jako je DevOps. Jeho použití ukládá omezení pro vaši aplikaci. Například nemůže přihlásit uživatele, který potřebuje provést vícefaktorové ověřování (podmíněný přístup). Vaše aplikace také nebude mít prospěch z jednotného přihlašování (SSO).

  Je to také proti zásadám moderního ověřování a je poskytováno pouze z důvodů staršíverze.

  ![Desktopová aplikace](media/scenarios/desktop-app.svg)

- Pokud napíšete přenosný nástroj příkazového řádku, pravděpodobně aplikaci .NET Core, která běží na Linuxu nebo Macu, a pokud přijmete, že ověřování bude delegováno do systémového prohlížeče, můžete použít interaktivní ověřování. .NET Core neposkytuje [webový prohlížeč](https://aka.ms/msal-net-uses-web-browser), takže ověřování se děje v systémovém prohlížeči. V opačném případě je nejlepší volbou v takovém případě použít tok kódu zařízení. Tento tok se používá také pro aplikace bez prohlížeče, jako jsou například aplikace IoT.

  ![Aplikace bez prohlížeče](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifika

Desktopové aplikace mají řadu specifik. Závisí hlavně na tom, zda vaše aplikace používá interaktivní ověřování nebo ne.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace Pro stolní počítače: Registrace aplikace](scenario-desktop-app-registration.md)
