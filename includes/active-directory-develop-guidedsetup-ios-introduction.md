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
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 8c7f11d74d0a0b81f9f0c40871b2eaa3eb25f51f
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988237"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Volání rozhraní Microsoft Graph API z aplikace pro iOS

Tento průvodce ukazuje, jak aplikace nativní aplikace pro iOS (Swift) může volat rozhraní API, která vyžadují přístupových tokenů z koncového bodu v2.0 Microsoft Azure Active Directory (Azure AD). Průvodce vysvětluje, jak získat přístupové tokeny a používat je ve volání do rozhraní Microsoft Graph API a další rozhraní API.

Po dokončení cvičení v této příručce, může volat chráněné rozhraní API z jakéhokoli společnosti nebo organizace, která má Azure AD. Aplikace může volat chráněné rozhraní API pomocí osobních účtů, jako je outlook.com, live.com a další, stejně jako pracovní nebo školní účty.

## <a name="prerequisites"></a>Požadavky

- Verze XCode 10.x se vyžaduje pro ukázku, která se vytvoří v této příručce. Si můžete stáhnout z XCode [iTunes webu](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adresa URL pro XCode stahování").
- [Carthage](https://github.com/Carthage/Carthage) správce závislostí, je třeba Správa balíčků.

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Jak funguje tento průvodce](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

V této příručce ukázkovou aplikaci umožňuje dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu Azure AD v2.0 aplikace pro iOS. V tomto scénáři token se přidá na požadavky HTTP s použitím **autorizace** záhlaví. Získání tokenu a obnovení jsou zpracovány pomocí Microsoft Authentication Library (MSAL).

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Získání tokenu pro přístup k chráněné webové rozhraní API pro zpracování

Jakmile se uživatel ověřuje, ukázkové aplikace získá token zabezpečení. Token, který se používá k dotazování rozhraní Microsoft Graph API nebo webové rozhraní API, která je zabezpečena pomocí koncového bodu Azure AD v2.0.

Rozhraní API, jako je například Microsoft Graphu, vyžadují přístupového tokenu pro povolení přístupu ke konkrétním prostředkům. Tokeny jsou potřeba ke čtení profilu uživatele, přístup ke kalendáři uživatele, odesílání e-mailu a tak dále. Aplikace může požadovat přístupový token s použitím MSAL a určením obory API. Přístupový token se přidá do HTTP **autorizace** záhlaví pro každé volání, která se provádí proti chráněnému prostředku.

Knihovna MSAL slouží ke správě ukládání do mezipaměti a aktualizaci přístupových tokenů za vás, takže vaše aplikace nemusí.

## <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovny:

|Knihovna|Popis|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library ve verzi Preview pro iOS|
