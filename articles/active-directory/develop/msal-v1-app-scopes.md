---
title: Obory pro aplikaci v 1.0 (Microsoft Authentication Library) | Azure
description: Seznamte se s obory pro aplikaci v 1.0 pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395b5105fdb067977f8414fe5675170032262fe0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921394"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro tokeny webového rozhraní API přijímající verze 1.0

Oprávnění OAuth2 jsou obory oprávnění, které aplikace Azure Active Directory (Azure AD) pro vývojáře webového rozhraní API (v 1.0) zpřístupňuje klientským aplikacím. Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu. Podívejte se na část o `oauth2Permissions` v [odkazu Azure Active Directory manifestu aplikace](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním OAuth2 oprávnění aplikace v 1.0

Chcete-li získat tokeny pro konkrétní obory aplikace v 1.0 (například graf Azure AD, který je https:\//graph.windows.net), je nutné vytvořit obory zřetězením požadovaného identifikátoru prostředku s požadovaným oprávněním OAuth2 pro daný prostředek.

Například pro přístup jménem uživatele a verze 1.0 webového rozhraní API, kde je identifikátor URI ID aplikace `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure AD pomocí služby Azure AD Graph API (https:\//graph.windows.net/), musíte vytvořit seznam oborů, jak je znázorněno v následujících příkladech:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Chcete-li zapsat rozsah odpovídající rozhraní Azure Resource Manager API (https:\//management.core.windows.net/), musíte požádat o následující obor (Všimněte si dvou lomítek):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Je potřeba použít dvě lomítka, protože rozhraní Azure Resource Manager API očekává lomítko v deklaraci identity cílové skupiny (AUD) a pak je lomítko oddělit název rozhraní API z oboru.

Logika, kterou používá služba Azure AD, je následující:

- Koncový bod pro ADAL (Azure AD v 1.0) s tokenem přístupu v 1.0 (jediný možný), AUD = Resource
- Pro MSAL (Microsoft Identity Platform (v 2.0)) koncový bod žádající o přístupový token pro prostředek přijímající tokeny v 2.0 `aud=resource.AppId`
- Pro MSAL (koncový bod verze 2.0) s žádostí o přístupový token pro prostředek, který přijímá přístupový token v 1.0 (což je výše uvedený případ výše), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku. Proto pokud https:\//database.windows.net očekává cílovou skupinu "https:\//database.windows.net/", budete muset požádat o obor https:\//database.windows.net//.default. Viz také problém GitHub [#747: koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání ověřování SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory pro vyžadování přístupu ke všem oprávněním aplikace v 1.0

Pokud chcete získat token pro všechny statické obory aplikace v 1.0, připojovat k identifikátoru URI ID aplikace v rozhraní API ". default":

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>Rozsahy, které se vyžadují pro aplikaci Flow/démon přihlašovacích údajů klienta

V případě toku přihlašovacích údajů klienta by se taky `/.default`obor, který se má předat. To oznamuje službě Azure AD: "všechna oprávnění na úrovni aplikace, ke kterým správce souhlasí v registraci aplikace.
