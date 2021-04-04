---
title: Použití modulu služby Azure Maps Services | Mapy Microsoft Azure
description: Seznamte se s modulem služeb Azure Maps Services. Podívejte se, jak načíst a použít tuto pomocnou knihovnu pro přístup k Azure Maps službě REST ve webových nebo Node.jsch aplikacích.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008595"
---
# <a name="use-the-azure-maps-services-module"></a>Použití modulu služby Azure Maps Services

Sada Azure Maps Web SDK poskytuje *modul služeb*. Tento modul je pomocná knihovna usnadňující použití Azure Maps služby REST ve webových nebo Node.js aplikacích pomocí JavaScriptu nebo TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Použití modulu služby na webové stránce

1. Vytvořte nový soubor HTML.
1. Načtěte modul Azure Maps Services. Můžete ho načíst jedním ze dvou způsobů:
    - Použijte globálně hostovanou verzi Content Delivery Network služby Azure Maps Services v rámci Azure. Přidejte odkaz na skript do `<head>` prvku souboru:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Případně můžete modul služby pro Azure Maps zdrojového kódu sady web SDK načíst místně pomocí balíčku [Azure-Maps-REST](https://www.npmjs.com/package/azure-maps-rest) npm a potom ho hostovat s vaší aplikací. Tento balíček obsahuje také definice TypeScript. Použijte tento příkaz:
    
        > **npm install azure-maps-rest**
    
        Pak přidejte odkaz na skript do `<head>` prvku souboru:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Vytvořte kanál ověřování. Aby bylo možné inicializovat koncový bod klienta adresy URL služby, musí být kanál vytvořen. Pomocí vlastního klíče účtu Azure Maps nebo přihlašovacích údajů Azure Active Directory (Azure AD) můžete ověřit klienta služby Azure Maps Search. V tomto příkladu se vytvoří klient URL vyhledávací služby. 

    Pokud pro ověřování používáte klíč předplatného:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Pokud používáte službu Azure AD k ověřování:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Další informace najdete v tématu [ověřování pomocí Azure Maps](azure-maps-authentication.md).

1. Následující kód používá nově vytvořený klient adresy URL služby Azure Maps Search k obnově kódu adresy: "1 Microsoft Way, Redmond, WA". Kód používá `searchAddress` funkci a zobrazí výsledky jako tabulku v těle stránky.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Tady je celý běžící ukázka kódu:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Použití modulu služby" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>pomocí modulu služby</a> Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Podpora cloudu Azure Government

Sada Azure Maps Web SDK podporuje cloud Azure Government. Všechny adresy URL JavaScriptu a CSS použité pro přístup k sadě Azure Maps Web SDK zůstávají stejné, ale následující úlohy se musí udělat pro připojení k Azure Government cloudové verzi Azure Maps platformy.

Při použití interaktivního ovládacího prvku mapy přidejte následující řádek kódu před vytvořením instance `Map` třídy. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Při ověřování map a služeb nezapomeňte použít podrobnosti ověřování Azure Maps z Azure Government cloudové platformy.

Při použití modulu služeb musí být při vytváření instance koncového bodu adresy URL rozhraní API nastavena doména pro služby. Například následující kód vytvoří instanci `SearchURL` třídy a odkazuje na doménu na cloud Azure Government.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Pokud máte přímý přístup ke službě Azure Maps REST, změňte doménu adresy URL na `atlas.azure.us` . Pokud například používáte službu rozhraní API pro hledání, změňte doménu adresy URL z `https://atlas.microsoft.com/search/` na `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Další ukázky kódu, které používají modul služby, najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Zobrazit výsledky hledání na mapě](./map-search-location.md)

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)