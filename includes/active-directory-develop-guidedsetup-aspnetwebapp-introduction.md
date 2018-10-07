---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 4512c02594fdfac96fbc785b13d8c992b4e57c52
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843339"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Přidání přihlašování s Microsoftem do webové aplikace ASP.NET

Tato příručka ukazuje, jak implementovat přihlášení u Microsoftu pomocí řešení ASP.NET MVC s tradiční webové aplikace založené na prohlížeči pomocí OpenID Connect. 

Na konci tohoto průvodce vaše aplikace budou moci přijímat přihlášení osobní účty (včetně outlook.com, live.com a další) a také pracovní a školní účty z jakéhokoli společnosti nebo organizace, která má integrované s Azure Active Directory. 

> Tato příručka vyžaduje Visual Studio 2015 Update 3 nebo Visual Studio 2017.  Nemáte ji?  [Stáhnout Visual Studio 2017 zdarma](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Jak funguje tento průvodce](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Ukázková aplikace vytvořené v této příručce vychází scénář, ve kterém uživatel používá prohlížeč pro přístup k žádosti o uživateli se přihlásit přes přihlašovací tlačítko webu ASP.NET. V tomto scénáři se většina aktivit vykreslení webové stránky odehrává na straně serveru.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware, který aplikaci umožňuje použít OpenIdConnect pro ověřování|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, který aplikaci umožňuje udržovat relaci uživatele pomocí souborů cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Aplikacím založeným na specifikaci OWIN umožňuje spouštět se ve službě IIS pomocí kanálu požadavků ASP.NET|

