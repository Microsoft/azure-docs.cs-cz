---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482338"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Přidání přihlašování s Microsoftem do webové aplikace ASP.NET

Tato příručka ukazuje, jak implementovat přihlášení u Microsoftu pomocí řešení ASP.NET MVC s tradiční webové aplikace založené na prohlížeči pomocí OpenID Connect.

Na konci tohoto průvodce budou moci přijímat přihlášení osobní účty, například outlook.com, live.com a ostatní aplikace. Tyto účty také zahrnují pracovní a školní účty z jakéhokoli společnosti nebo organizace, která má integrované s Azure Active Directory.

> Tato příručka vyžaduje Visual Studio 2019.  Nemáte ji?  [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Ukázkové aplikace, které vytvoříte je založený na scénáři, kde použít prohlížeč pro přístup k webu ASP.NET, která požaduje uživateli se přihlásit přes přihlašovací tlačítko. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který aplikaci umožňuje udržovat relaci uživatele pomocí souborů cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Aplikacím založeným na specifikaci OWIN umožňuje spouštět se ve službě IIS pomocí kanálu požadavků ASP.NET|
