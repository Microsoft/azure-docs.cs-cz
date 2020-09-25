---
title: Uložení ověřovacího tokenu do mezipaměti
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak token ověřování ukládat do mezipaměti.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 250fe6279288002fad1b6c12b158ffae3d83c264
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318853"
---
# <a name="how-to-cache-the-authentication-token"></a>Jak uložit ověřovací token do mezipaměti

Tento článek ukazuje, jak uložit ověřovací token do mezipaměti za účelem zvýšení výkonu aplikace.

## <a name="using-aspnet"></a>Použití ASP.NET

Importujte balíček NuGet **Microsoft. IdentityModel. clients. Active** , který se používá k získání tokenu. V dalším kroku použijte následující kód k získání `AuthenticationResult` a pomocí hodnot ověřování, které jste dostali při [vytváření prostředku pro moderní čtečku](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

`AuthenticationResult`Objekt má vlastnost, `AccessToken` která je skutečným tokenem, který použijete při spouštění moderního čtecího zařízení pomocí sady SDK. Obsahuje také vlastnost, `ExpiresOn` která označuje, kdy vyprší platnost tokenu. Před spuštěním moderního čtecího zařízení můžete zjistit, jestli vypršela platnost tokenu, a získat nový token, jenom pokud vypršela jeho platnost.

## <a name="using-nodejs"></a>Použití Node.JS

Přidejte do projektu balíček [**Request**](https://www.npmjs.com/package/request) npm. Použijte následující kód k získání tokenu pomocí hodnot ověřování, které jste dostali při [vytváření prostředku pro moderní čtečku](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

`expires_on`Vlastnost je datum a čas, kdy vyprší platnost tokenu, vyjádřený jako počet sekund od 1. ledna 1970 UTC. Tuto hodnotu použijte k určení, jestli vypršela platnost tokenu, než se pokusí získat nový.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)