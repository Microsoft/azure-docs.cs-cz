---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: e99d4f6676795a846b48ccd2a08856f4d32f269f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205633"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Volání Microsoft Graph API z aplikace na ploše systému Windows

Tato příručka ukazuje, jak získat přístupový token a volání rozhraní Graph API Microsoft nebo jiná rozhraní API, které vyžadují přístupové tokeny z koncového bodu Azure Active Directory v2 nativní aplikace Windows Desktop .NET (XAML).

Po dokončení průvodce, aplikace bude moci volat chráněné rozhraní API, které používá osobní účty (včetně live.com, outlook.com a dalších). Aplikace se také použít pracovní a školní účty z jakékoli společnosti nebo organizace, která využívá Azure Active Directory.  

> [!NOTE] 
> V Průvodci vyžaduje Visual Studio 2015 Update 3 nebo Visual Studio 2017.  Nemáte buď tyto verze? [Stáhněte si Visual Studio 2017 zdarma](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak funguje ukázková aplikace generované tímto průvodcem

![Jak funguje tato příručka](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Ukázkové aplikace, které vytvoříte v této příručce umožňuje aplikace Windows Desktop, který se dotazuje rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu v2 Azure Active Directory. V tomto scénáři přidat token na požadavky HTTP přes autorizační hlavičky. Microsoft ověřování knihovny (MSAL) zpracovává získání tokenu a obnovení.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Zpracování získání tokenu pro přístup k chráněné webové rozhraní API

Po ověření uživatele je ukázková aplikace obdrží token, který slouží k dotazu Microsoft Graph API nebo webového rozhraní API, která je zabezpečená službou Azure Active Directory v2.

Rozhraní API, jako je například Microsoft Graph vyžadují token pro povolení přístupu ke konkrétním prostředkům. Token je například potřeba čtení profilu uživatele, přístup k kalendáře uživatele nebo odeslání e-mailu. Aplikace může požadovat token přístupu pomocí MSAL k těmto prostředkům přistupovat tak, že zadáte obory rozhraní API. Tento přístupový token se pak přidá do hlavičky HTTP autorizace pro každé volání, které se provádí před k chráněnému prostředku. 

MSAL spravuje ukládání do mezipaměti a obnovení přístupových tokenů pro vás, tak, aby vaše aplikace nemusí.

## <a name="nuget-packages"></a>Balíčky NuGet

Tato příručka používá následující balíčky NuGet:

|Knihovna|Popis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Knihovna Microsoft ověřování (MSAL)|

