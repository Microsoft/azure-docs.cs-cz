---
title: Obory pro aplikace v 1.0 (MSAL) | Azure
description: Seznamte se s obory pro aplikaci v 1.0 pomocí knihovny Microsoft Authentication Library (MSAL).
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81536178"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro tokeny webového rozhraní API přijímající verze 1.0

Oprávnění OAuth2 jsou obory oprávnění, které aplikace Azure Active Directory (Azure AD) pro vývojáře webového rozhraní API (v 1.0) zpřístupňuje klientským aplikacím. Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu. Informace najdete v části `oauth2Permissions` v tématu [Azure Active Directory Reference k manifestu aplikace](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním OAuth2 oprávnění aplikace v 1.0

Chcete-li získat tokeny pro konkrétní obory aplikace v 1.0 (například rozhraní Microsoft Graph API, což je https://graph.microsoft.com) , vytvořte obory zřetězením požadovaného identifikátoru prostředku s požadovaným oprávněním OAuth2 pro daný prostředek.

Například pro přístup jménem uživatele a verze 1.0 webového rozhraní API, kde identifikátor URI ID aplikace je `ResourceId` :

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure AD pomocí rozhraní API Microsoft Graph (https: \/ /Graph.Microsoft.com/), musíte vytvořit seznam oborů, jak je znázorněno v následujících příkladech:

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Chcete-li zapsat rozsah odpovídající rozhraní Azure Resource Manager API (https: \/ /Management.Core.Windows.NET/), musíte požádat o následující obor (Všimněte si dvou lomítek):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Je potřeba použít dvě lomítka, protože rozhraní Azure Resource Manager API očekává lomítko v deklaraci identity cílové skupiny (AUD) a pak je lomítko oddělit název rozhraní API z oboru.

Logika, kterou používá služba Azure AD, je následující:

- Koncový bod pro ADAL (Azure AD v 1.0) s tokenem přístupu v 1.0 (jediný možný), AUD = Resource
- Pro MSAL (Microsoft Identity Platform (v 2.0)) koncový bod žádající o přístupový token pro prostředek, který přijímá tokeny v 2.0, `aud=resource.AppId`
- Pro MSAL (koncový bod verze 2.0) s žádostí o přístupový token pro prostředek, který přijímá přístupový token v 1.0 (což je výše uvedený případ výše), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku. Proto pokud https: \/ /Database.Windows.NET očekává cílovou skupinu "https: \/ /Database.Windows.NET/", budete muset požádat o obor "https: \/ /Database.Windows.NET//.default". Viz také problém GitHub [#747: koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání ověřování SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

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

V případě toku přihlašovacích údajů klienta by měl být taky obor, který se má předat `/.default` . To oznamuje službě Azure AD: "všechna oprávnění na úrovni aplikace, ke kterým správce souhlasí v registraci aplikace.
