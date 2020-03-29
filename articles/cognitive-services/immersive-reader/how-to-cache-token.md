---
title: Uložení ověřovacího tokenu do mezipaměti
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak ukládat ověřovací token do mezipaměti.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946163"
---
# <a name="how-to-cache-the-authentication-token"></a>Jak ukládat ověřovací token do mezipaměti

Tento článek ukazuje, jak ukládat do mezipaměti ověřovací token za účelem zlepšení výkonu vaší aplikace.

## <a name="using-aspnet"></a>Použití ASP.NET

Importujte balíček **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, který se používá k získání tokenu. Dále použijte následující kód k `AuthenticationResult`získání , pomocí ověřovacích hodnot, které jste získali při [vytváření prostředku Immersive Reader](./how-to-create-immersive-reader.md).

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Objekt `AuthenticationResult` má `AccessToken` vlastnost, která je skutečný token, který budete používat při spouštění immersive Reader pomocí sady SDK. Má také `ExpiresOn` vlastnost, která označuje, kdy vyprší platnost tokenu. Před spuštěním immersive Reader, můžete zkontrolovat, zda platnost tokenu vypršela, a získat nový token pouze v případě, že vypršela.

## <a name="using-nodejs"></a>Použití souboru Node.JS

Přidejte [**balíček npm požadavku**](https://www.npmjs.com/package/request) do projektu. Pomocí následujícího kódu získáte token pomocí ověřovacích hodnot, které jste získali při [vytváření prostředku immersive Reader](./how-to-create-immersive-reader.md).

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

Vlastnost `expires_on` je datum a čas, kdy vyprší platnost tokenu, vyjádřený jako počet sekund od 1. Pomocí této hodnoty můžete určit, zda platnost tokenu vypršela před pokusem o získání nového.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Další kroky

* Seznamte se s [referenční sadou Immersive Reader SDK](./reference.md)