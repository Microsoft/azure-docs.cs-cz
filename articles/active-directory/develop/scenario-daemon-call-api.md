---
title: Volání webového rozhraní API z aplikace daemon - platforma identit Microsoft | Azure
description: Přečtěte si, jak vytvořit aplikaci pro daemon, která volá webová API
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
ms.openlocfilehash: 338b638d6b33bcbbb5cf377643a96c71b0d314bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76775198"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikace Daemon, která volá webová rozhraní API – volání webového rozhraní API z aplikace

Aplikace daemonu .NET mohou volat webové rozhraní API. Aplikace daemonu .NET mohou také volat několik předem schválených webových rozhraní API.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Volání webového rozhraní API z aplikace daemon

Tady je postup, jak použít token k volání rozhraní API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

## <a name="calling-several-apis"></a>Volání několika api

Pro aplikace daemon, webová api, které voláte musí být předem schválen. Neexistuje žádný přírůstkový souhlas s aplikacemi pro daemon. (Neexistuje žádná interakce s uživatelem.) Správce klienta musí předem poskytnout souhlas pro aplikaci a všechna oprávnění rozhraní API. Pokud chcete volat několik api, musíte získat token pro každý prostředek, při každém volání `AcquireTokenForClient`. MSAL bude používat mezipaměť tokenu aplikace, aby se zabránilo zbytečným voláním služby.

## <a name="next-steps"></a>Další kroky

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon app - přejít do výroby](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon app - přejít do výroby](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon app - přejít do výroby](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
