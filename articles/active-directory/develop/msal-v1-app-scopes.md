---
title: Obory pro aplikaci v 1.0 (Microsoft Authentication Library) | Azure
description: Seznamte se s obory pro aplikaci v 1.0 pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0590614e1c1bc7331246e76fa26a6567a05324e6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532333"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Obory pro tokeny webového rozhraní API přijímající verze 1.0

Oprávnění OAuth2 jsou obory oprávnění, které aplikace webového rozhraní API (v 1.0) zpřístupňuje webovým aplikacím v Azure AD pro vývojáře (v 1.0). Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu. Informace najdete `oauth2Permissions` v části v tématu [Azure Active Directory Reference k manifestu aplikace](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory pro vyžádání přístupu ke konkrétním OAuth2 oprávnění aplikace v 1.0
Pokud chcete získat tokeny pro konkrétní obory aplikace v 1.0 (například Azure AD Graph, která je https:/Graph.Windows.NET),\/musíte vytvořit obory zřetězením požadovaného identifikátoru prostředku s požadovaným oprávněním OAuth2. pro tento prostředek.

Například pro přístup jménem uživatele a verze 1.0 webového rozhraní API, kde identifikátor URI ID aplikace je `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure Active Directory pomocí rozhraní Azure AD Graph API (https:\//Graph.Windows.NET/), měli byste vytvořit seznam oborů, jak je uvedeno níže:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Pokud chcete zapsat rozsah odpovídající rozhraní Azure Resource Manager API (https:\//Management.Core.Windows.NET/), musíte požádat o následující obor (Všimněte si dvou lomítek):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Je potřeba použít dvě lomítka, protože rozhraní Azure Resource Manager API očekává lomítko v deklaraci identity cílové skupiny (AUD) a pak je lomítko oddělit název rozhraní API z oboru.

Logika, kterou používá služba Azure AD, je následující:

- Koncový bod pro ADAL (v 1.0) s přístupovým tokenem v 1.0 (jediný možný), AUD = Resource
- Pro MSAL (koncový bod Microsoft Identity Platform (v 2.0)) s žádostí o přístupový token pro prostředek přijímá tokeny v 2.0, AUD = Resource. Identifikátor
- Pro MSAL (koncový bod verze 2.0), který žádá o přístupový token pro prostředek, který přijímá přístupový token v 1.0 (což je výše uvedený případ), Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku. Proto pokud https:\//Database.Windows.NET očekává cílovou skupinu "https:\//Database.Windows.NET/", budete muset požádat o obor "https:\//Database.Windows.NET//.default". Viz také #747 problému [na GitHubu: Koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)ověřování SQL.

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

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Obory pro požadavek na aplikaci Flow/démon přihlašovacích údajů klienta
V případě toku přihlašovacích údajů klienta by měl být `/.default`taky obor, který se má předat. To oznamuje službě Azure AD: "všechna oprávnění na úrovni aplikace, ke kterým správce souhlasí v registraci aplikace.
