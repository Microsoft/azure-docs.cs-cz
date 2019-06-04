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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae17ef749a353cd60227e31ba4dadf328b1dc935
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482450"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Volání rozhraní Microsoft Graph API z desktopové aplikace Windows

Tato příručka ukazuje, jak nativní aplikace pro Windows Desktop .NET (XAML) používá přístupového tokenu pro volání rozhraní Microsoft Graph API. Aplikace se dostanete také další rozhraní API, které vyžadují přístupových tokenů z platforma identit Microsoft pro vývojáře koncový bod verze 2.0. Tato platforma dřív označovala jako Azure AD.

Po dokončení průvodce, vaše aplikace bude moct volat chráněné rozhraní API, které používá osobní účty (včetně outlook.com, live.com a další). Aplikace bude také použít pracovní a školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.  

> [!NOTE]
> V Průvodci vyžaduje Visual Studio 2015 Update 3 nebo Visual Studio 2019. Nemáte některou z těchto verzí? [Stáhněte si Visual Studio 2019 zdarma](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázkové aplikace vygenerované v této příručce

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Ukázkovou aplikaci, kterou vytvoříte v této příručce umožňuje aplikaci Windows Desktop, který se dotazuje rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu platforma identit Microsoft. Pro tento scénář přidejte token na požadavky HTTP přes autorizační hlavičky. Microsoft Authentication Library (MSAL) zpracovává získání tokenu a obnovení.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Zpracování získání tokenu pro přístup k chráněné webové rozhraní API

Po ověření uživatele je ukázková aplikace přijme token, který můžete použít k dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, která je zabezpečena pomocí platforma identit Microsoft pro vývojáře.

Rozhraní API, jako je například Microsoft Graphu vyžaduje token pro povolení přístupu ke konkrétním prostředkům. Token je například potřeba čtení profilu uživatele, přístup uživatele kalendáře nebo odeslat e-mailu. Aplikace může požadovat přístupový token s použitím MSAL k těmto prostředkům přistupovat zadáním obory API. Tento přístupový token se pak přidá do hlavičky autorizace HTTP pro každé volání, která se provádí proti chráněnému prostředku.

Knihovna MSAL spravuje ukládání do mezipaměti a aktualizaci přístupových tokenů pro vás, tak, aby vaše aplikace nemusí.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
