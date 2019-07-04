---
title: Obory aplikace verze 1.0 (knihovna Microsoft Authentication Library) | Azure
description: Další informace o oborech v1.0 aplikací pomocí Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e43bc245a5908ba1bf91e7b4bee6df2f5cfc618
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514362"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Oborů pro webové rozhraní API přijímat tokeny v1.0

Obory oprávnění, které Azure AD pro vývojáře (verze 1.0) aplikaci rozhraní API (prostředek) uvádí pro klientské aplikace jsou oprávnění OAuth2. Tyto obory oprávnění může udělit klientské aplikace během souhlas. V části `oauth2Permissions` v [odkaz na manifest aplikace Azure Active Directory](reference-app-manifest.md#manifest-reference).

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Obory požádáte o přístup k určité oprávnění OAuth2 aplikace v1.0
Pokud chcete získat tokeny pro konkrétní obory v1.0 aplikace (třeba Azure AD graph, což je protokol https:\//graph.windows.net), je potřeba vytvořit obory zřetězením identifikátor požadovaný prostředek se požadované oprávnění OAuth2 pro daný prostředek.

Například pro přístup jménem uživatele webové rozhraní API v1.0 kde je identifikátor URI ID aplikace `ResourceId`:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Pokud chcete číst a zapisovat pomocí MSAL.NET Azure Active Directory pomocí služby Azure AD graph API (protokol https:\//graph.windows.net/), vytvořili byste seznam oborů, viz následující příklad:

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Pokud chcete zadat rozsahu odpovídající rozhraní API Azure Resource Manageru (protokol https:\//management.core.windows.net/), budete muset požádat o k následujícímu oboru (Poznámka: dvě lomítka):

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Budete muset použít dvě lomítka, protože rozhraní API Azure Resource Manageru očekává, že lomítko v jeho deklarace identity cílové skupiny (aud) a pak je lomítko pro oddělení název rozhraní API z oboru.

Logikou používanou službou Azure AD je následující:

- Pro koncový bod ADAL (verze 1.0) verze 1.0 přístupového tokenu (jediné možné), aud = prostředků
- Pro požadující přístupového tokenu prostředku přijímat tokeny v2.0, aud MSAL (Microsoft identity platform (v2.0) endpoint) = prostředků. ID aplikace
- Azure AD pro MSAL (koncový bod verze 2.0) s dotazem přístupového tokenu pro určitý prostředek, který přijímá token přístupu verze 1.0 (což se v případě výše), analyzuje požadovanou cílovou skupinu z požadovaného oboru převzetím všechno dřív než poslední lomítko a používat jej jako identifikátor prostředku. Proto pokud https:\//database.windows.net očekává, že cílovou skupinou "https:\//database.windows.net/", budete muset požádat o oboru "https:\//database.windows.net//.default". Viz také Githubu problém [#747: Adresa url prostředku koncového lomítka vynecháte, která způsobila chybu při ověřování sql](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Obory požádáte o přístup k všechna oprávnění aplikace v1.0
Pokud chcete získat token pro všechny statické obory aplikace v1.0, připojte k rozhraní API pro identifikátor URI ID aplikace "hodnotu .default":

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-client-credential-flow--daemon-app"></a>Obory požadavku pro klienta přihlašovacích údajů tok / démon aplikace
V případě tok přihlašovacích údajů klienta, by také být oboru předat `/.default`. To říká do služby Azure AD: "všechny aplikace úrovně oprávnění, která správce vyjádřil souhlas se v registraci aplikace.
