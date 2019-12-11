---
title: Volání webového rozhraní API z aplikace démona – Microsoft Identity Platform | Azure
description: Naučte se vytvářet aplikace démona, která volá webová rozhraní API.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f1676ab1bdcf2b23907824bb9bf543b5f28ce6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962606"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikace démona, která volá webové rozhraní API – volání webového rozhraní API z aplikace

Aplikace typu démon může volat webové rozhraní API z aplikace démona .NET nebo volat několik předběžně schválených webových rozhraní API.

## <a name="calling-a-web-api-daemon-application"></a>Volání aplikace webového rozhraní API démona

Tady je postup, jak použít token pro volání rozhraní API:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Volání několika rozhraní API

Pro aplikace démona musí být webová rozhraní API, která zavoláte, předem schválená. Nedojde k žádnému přírůstkového souhlasu s aplikacemi démona (žádná interakce s uživatelem). Správce tenanta musí aplikace a všechna oprávnění API předem odsouhlasit. Pokud chcete volat několik rozhraní API, budete muset získat token pro každý prostředek při každém volání `AcquireTokenForClient`. MSAL použije mezipaměť tokenu aplikace, aby nedocházelo k zbytečným voláním služby.

## <a name="next-steps"></a>Další kroky

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikace démon – přesun do produkčního prostředí](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikace démon – přesun do produkčního prostředí](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikace démon – přesun do produkčního prostředí](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
