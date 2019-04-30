---
title: Použít modul services – Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít modul služby Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: f3650d4db06a763308939e9fb1a98fddb0eaa04a
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738768"
---
# <a name="use-the-azure-maps-services-module"></a>Použít modul služby Azure Maps

Poskytuje sadu SDK Azure Maps webové *modul services*. Tento modul je pomocné knihovny, který usnadňuje použití služby Azure Maps REST ve webové nebo aplikací v Node.js s použitím jazyka JavaScript nebo TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Použít modul služby na webové stránce

1. Vytvořte nový soubor HTML.
1. Načtení modulu služby Azure Maps. Můžete načíst v jednom ze dvou způsobů:
    - Použijte globálně prostředí Azure Content Delivery Network verze modulu služby Azure Maps. Přidejte odkaz na skript `<head>` prvek souboru:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - Můžete také načíst zdrojový kód sady SDK webové mapy Azure místně pomocí [klidovém stavu azure maps](https://www.npmjs.com/package/azure-maps-rest) npm balíček a hostujte ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript. Použijte tento příkaz:
    
        > **npm install azure map – rest**
    
        Pak přidejte odkaz na skript `<head>` prvek souboru:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Vytvoření kanálu ověřování. Kanál musíte vytvořit předtím, než bude možné inicializovat klienta koncový bod adresy URL služby. Použijte vlastní klíč účtu Azure Maps nebo přihlašovacích údajů Azure Active Directory (Azure AD) k ověření klienta služby Azure Search mapy. V tomto příkladu se vytvoří adresa URL klienta služby Search. 

    Pokud použijete klíč předplatného pro ověřování:

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

    Další informace najdete v tématu [ověřování pomocí služby Azure Maps](azure-maps-authentication.md).

1. Následující kód používá nově vytvořený klienta adresy URL služby Azure Search k geokód adresu: "1 Microsoft způsobem, Redmond, WA". Tento kód použije `searchAddress` fungovat a zobrazí výsledky jako tabulku do těla stránky.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
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

    Tady je kompletní spuštěním ukázky kódu:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pomocí modulu služby" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>pomocí modulu služby</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

Další ukázky kódu, které používají modul služby najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Na mapě zobrazit výsledky hledání](./map-search-location.md)

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)