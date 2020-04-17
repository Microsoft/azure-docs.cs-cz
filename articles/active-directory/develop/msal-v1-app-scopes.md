---
title: Obory pro aplikace v1.0 (MSAL) | Azure
description: Informace o oborech pro aplikaci v1.0 pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 61d07c1ba912a0e24b2f4e5fa67243b4525db367
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536178"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro webové rozhraní API, které přijímá tokeny v1.0

Oprávnění OAuth2 jsou obory oprávnění, které azure active directory (Azure AD) pro vývojáře (v1.0) webové rozhraní API (prostředek) aplikace zpřístupňuje klientských aplikací. Tyto obory oprávnění mohou být uděleny klientským aplikacím během souhlasu. Podívejte se `oauth2Permissions` na část o manifestu [aplikace Azure Active Directory .](reference-app-manifest.md#manifest-reference)

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním oprávněním OAuth2 aplikace v1.0

Chcete-li získat tokeny pro konkrétní obory aplikace v1.0 (například rozhraní Microsoft Graph API, což je https://graph.microsoft.com), vytvořte obory zřetězením požadovaného identifikátoru prostředku s požadovaným oprávněním OAuth2 pro daný prostředek.

Chcete-li například získat přístup jménem uživatele k webovému rozhraní API `ResourceId`v1.0, kde je identifikátor URI ID aplikace:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Chcete-li číst a zapisovat pomocí MSAL.NET\/Azure AD pomocí rozhraní Microsoft Graph API (https: /graph.microsoft.com/), musíte vytvořit seznam oborů, jak je znázorněno v následujících příkladech:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Chcete-li napsat obor odpovídající rozhraní API\/Správce prostředků Azure (https: /management.core.windows.net/), musíte požádat o následující obor (všimněte si dvou lomítka):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Musíte použít dvě lomítka, protože rozhraní API Azure Resource Manager očekává lomítko v jeho deklarace cílové skupiny (aud) a pak je lomítko oddělit název rozhraní API od oboru.

Logika používaná službou Azure AD je následující:

- Pro koncový bod ADAL (Azure AD v1.0) s přístupovým tokenem v1.0 (jediný možný), aud= prostředek
- Pro koncový bod MSAL (Platforma identit y Microsoft (v2.0)) žádající přístupový token pro tokeny v2.0 pro příjem prostředků,`aud=resource.AppId`
- Pro MSAL (v2.0 koncový bod) žádají přístupový token pro prostředek, který přijímá přístupový token v1.0 (což je případ výše), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že vše před poslední lomítko a jeho použití jako identifikátor prostředku. Proto pokud https:\//database.windows.net očekává publikum "https:\//database.windows.net/", budete muset požádat o\/rozsah "https: /database.windows.net//.default". Viz také GitHub problém [#747: Prostředek url koncové lomítko je vynechán, což způsobilo selhání sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke všem oprávněním aplikace v1.0

Pokud chcete získat token pro všechny statické obory aplikace v1.0, připojte ".default" k URI IDENTIFIKÁTOR APLIKACE rozhraní API:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Obory pro vyžádání aplikace toku nebo daemonu pověření klienta

V případě toku pověření klienta by byl `/.default`také obor, který by měl být . To říká Azure AD: "všechna oprávnění na úrovni aplikace, které správce souhlasil v registraci aplikace.
