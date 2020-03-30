---
title: Použití modulu Služby Azure Maps Services | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak využít služby Microsoft Azure Maps REST pomocí modulu služeb Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988732"
---
# <a name="use-the-azure-maps-services-module"></a>Použití modulu služeb Azure Maps

Webová sada Azure Maps Web SDK poskytuje *modul služeb*. Tento modul je pomocná knihovna, která usnadňuje používání služeb Azure Maps REST ve webových aplikacích nebo aplikacích Node.js pomocí JavaScriptu nebo TypeScriptu.

## <a name="use-the-services-module-in-a-webpage"></a>Použití modulu služby na webové stránce

1. Vytvořte nový soubor HTML.
1. Načtěte modul služeb Azure Maps. Můžete jej načíst jedním ze dvou způsobů:
    - Použijte globálně hostovizované, Azure Content Delivery Network verze modulu služeb Azure Maps. Přidejte odkaz na `<head>` skript k prvku souboru:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Případně načtěte modul služeb pro zdrojový kód Azure Maps Web SDK místně pomocí balíčku [npm azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) a pak ho hostujte pomocí aplikace. Tento balíček také obsahuje definice jazyka TypeScript. Pomocí tohoto příkazu:
    
        > **npm install azure-maps-rest**
    
        Potom přidejte odkaz na `<head>` skript k prvku souboru:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Vytvořte kanál ověřování. Před inicializaci koncového bodu klienta adresy URL služby je nutné vytvořit kanál. K ověření klienta služby Azure Maps Search použijte vlastní klíč účtu Azure Maps nebo přihlašovací údaje služby Azure Active Directory (Azure AD). V tomto příkladu bude vytvořen klient adresy URL vyhledávací služby. 

    Pokud používáte klíč předplatného pro ověřování:

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

    Pokud používáte Azure AD pro ověřování:

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

    Další informace najdete [v tématu Ověřování pomocí Map Azure](azure-maps-authentication.md).

1. Následující kód používá nově vytvořené holokaustu Azure Maps Search url klienta geocode adresu: "1 Microsoft Way, Redmond, WA". Kód používá `searchAddress` funkci a zobrazuje výsledky jako tabulku v textu stránky.

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

    Zde je úplný, spuštěný ukázkový kód:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Použití modulu služby" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Podívejte se na pero <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>pomocí modulu služeb pomocí</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Cloudová podpora Azure Government

Webová sada Azure Maps Web SDK podporuje cloud Azure Government. Všechny adresy URL JavaScriptu a CSS používané pro přístup k webové sdk Azure Maps zůstávají stejné, ale pro připojení k cloudové verzi Azure Government platformy Azure Maps je potřeba provést následující úkoly.

Při použití interaktivního ovládacího prvku mapy přidejte před `Map` vytvořením instance třídy následující řádek kódu. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Nezapomeňte při ověřování mapy Azure použít podrobnosti o ověřování z cloudové platformy Azure Government.

Při použití modulu služby, doménu pro služby je třeba nastavit při vytváření instance koncového bodu URL rozhraní API. Například následující kód vytvoří instanci `SearchURL` třídy a odkazuje doménu na cloud Azure Government.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Pokud máte přímý přístup ke službám Azure `atlas.azure.us`Maps REST, změňte doménu URL na . Pokud například používáte službu rozhraní API `https://atlas.microsoft.com/search/` pro `https://atlas.azure.us/search/`vyhledávání, změňte doménu adresy URL z na .

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Adresa Mapy URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Vyhledávací adresa URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Routeurl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

Další ukázky kódu, které používají modul služby, naleznete v těchto článcích:

> [!div class="nextstepaction"]
> [Zobrazit výsledky hledání na mapě](./map-search-location.md)

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)
