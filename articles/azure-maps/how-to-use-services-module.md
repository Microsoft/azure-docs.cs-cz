---
title: Pomocí modulu Services – Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít modul služby Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e89a4675f867e53c499bb82b239ddb9bec1aed6f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521195"
---
# <a name="using-the-azure-maps-services-module"></a>Pomocí modulu služby Azure Maps

Azure Maps Web SDK poskytuje služby modulu, který je pomocné knihovny, který usnadňuje použití služby Azure Maps REST ve webové nebo aplikací v Node.js pomocí jazyka JavaScript nebo TypeScript.

## <a name="using-the-services-module-in-a-web-page"></a>Pomocí modulu services na webové stránce

1. Vytvořte nový soubor HTML.
2. Načíst v modulu služby Azure Maps. To lze provést jedním ze dvou možností;

    a. Použít globálně hostované CDN verzi modulu Azure Maps služby tak, že přidáte odkaz na skript `<head>` prvek souboru:
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
    ```
    
    b. Můžete také načíst místně pomocí sady SDK webové mapy Azure zdrojový kód [klidovém stavu azure maps](https://www.npmjs.com/package/azure-maps-rest) NPM balíček a hostujte ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript.
    
    > npm install azure map – rest
    
    Pak přidejte odkaz na skript `<head>` prvek souboru:
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. Chcete-li inicializovat klienta koncový bod adresy URL služby, musíte nejdřív vytvořit kanál ověřování. Použijte vlastní klíč účtu Azure Maps nebo přihlašovací údaje Azure Active Directory (AAD) k ověření klienta služby search. V tomto příkladu se vytvoří adresa URL klienta služby search. Pokud používáte klíč předplatného pro ověřování:

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    Pokud se používá pro ověřování Azure Active Directory (AAD):

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
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
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
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
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Další informace najdete v tématu [ověřování pomocí služby Azure Maps](azure-maps-authentication.md).

4. Následující kód používá klient adresa URL služby nově vytvořené vyhledávání geokód na adresu, pomocí "1, Microsoft, způsob, Redmond, WA" `searchAddress` fungovat a zobrazí výsledky jako tabulku do těla stránky. 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
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
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Toto je plně spuštěné ukázka kódu:

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

Naleznete v následujících článcích pro další ukázky kódu, které používají modul služby:

> [!div class="nextstepaction"]
> [Na mapě zobrazit výsledky hledání](./map-search-location.md)

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)