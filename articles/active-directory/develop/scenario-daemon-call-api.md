---
title: Volání webového rozhraní API z aplikace démona | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit aplikaci démona, která volá webové rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578392"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikace démona, která volá webové rozhraní API – volání webového rozhraní API z aplikace

Aplikace démona rozhraní .NET může volat webové rozhraní API. Aplikace démona rozhraní .NET může také volat několik předběžně schválených webových rozhraní API.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Volání webového rozhraní API z aplikace démona

Tady je postup, jak použít token pro volání rozhraní API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

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

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Pomocí klienta HTTP, jako je [Axios](https://www.npmjs.com/package/axios), zavolejte identifikátor URI koncového bodu rozhraní API s přístupovým tokenem jako držitel *autorizace*.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>Volání několika rozhraní API

Pro aplikace démona musí být webová rozhraní API, která zavoláte, předem schválená. Pro aplikace démona není k dispozici žádný přírůstkový souhlas. (Žádná interakce s uživatelem.) Správce tenanta musí pro aplikaci a všechna oprávnění rozhraní API poskytnout předem souhlas. Pokud chcete volat několik rozhraní API, Získejte token pro každý prostředek pokaždé, když se zavolá `AcquireTokenForClient` . MSAL použije mezipaměť tokenu aplikace, aby nedocházelo k zbytečným voláním služby.

## <a name="next-steps"></a>Další kroky

# <a name="net"></a>[.NET](#tab/dotnet)

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](./scenario-daemon-production.md?tabs=dotnet)prostředí.

# <a name="java"></a>[Java](#tab/java)

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](./scenario-daemon-production.md?tabs=java)prostředí.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](./scenario-daemon-production.md?tabs=nodejs)prostředí.

# <a name="python"></a>[Python](#tab/python)

Přejděte k dalšímu článku v tomto scénáři, [přejděte do produkčního](./scenario-daemon-production.md?tabs=python)prostředí.

---
