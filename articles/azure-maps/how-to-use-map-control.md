---
title: Začínáme s ovládacím prvkem webového mapování | Mapy Microsoft Azure
description: Naučte se používat mapu Microsoft Azure map mapy JavaScriptu na straně klienta k vykreslování map a vložených Azure Maps funkcí do vaší webové nebo mobilní aplikace.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335237"
---
# <a name="use-the-azure-maps-map-control"></a>Použití ovládacího prvku Azure Maps mapa

Ovládací prvek Mapa knihovna JavaScriptu na straně klienta umožňuje vykreslovat mapy a vložené Azure Maps funkce do vaší webové nebo mobilní aplikace.

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapu můžete vložit do webové stránky pomocí Ovládací prvek Mapa knihovny JavaScript na straně klienta.

1. Vytvořte nový soubor HTML.

2. Načtěte v sadě Azure Maps Web SDK. Můžete zvolit jednu ze dvou možností:

    * Pomocí globálně hostované verze CDN sady Azure Maps Web SDK přidejte odkazy na JavaScript a šablonu stylů v `<head>` elementu souboru HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Načtěte zdrojový kód Azure Maps Web SDK místně pomocí balíčku [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm a hostovat ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript.

        > **NPM instalace Azure-Maps-Control**

       Pak přidejte odkazy na Azure Maps šablon stylů a odkazů na zdroj skriptu k `<head>` elementu souboru:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Definice TypeScript lze do aplikace importovat přidáním následujícího kódu:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Chcete-li vykreslit mapu, aby vyplnila celé tělo stránky, přidejte do `<style>` `<head>` elementu následující element.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. V těle stránky přidejte `<div>` prvek a poskytněte mu `id` **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Pro inicializaci mapového ovládacího prvku definujte v těle HTML novou značku skriptu. Při vytváření instance `id` `Map` třídy předejte `<div>` v mapě `HTMLElement` nebo (například `document.getElementById('myMap')`) jako první parametr. Použijte vlastní klíč účtu Azure Maps nebo přihlašovací údaje pro Azure Active Directory (AAD) k ověření mapování pomocí [možností ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). 

   Pokud potřebujete vytvořit účet nebo najít klíč, postupujte podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   Možnost **Language** určuje jazyk, který má být použit pro popisky a ovládací prvky mapy. Další informace o podporovaných jazycích najdete v tématu [podporované jazyky](supported-languages.md). Pokud k ověřování používáte klíč předplatného, použijte následující:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   Pokud pro ověřování používáte Azure Active Directory (AAD), použijte následující:

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) se Azure Maps najdete [tady](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Další informace najdete v dokumentu [ověřování pomocí Azure Maps](azure-maps-authentication.md) a také [Azure Maps UKÁZEK ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Volitelně můžete najít následující prvky meta značky do hlavní stránky, která je užitečná:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Celý soubor HTML by měl vypadat přibližně takto:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Otevřete soubor ve webovém prohlížeči a zobrazte vykreslenou mapu. Měl by vypadat jako na následujícím obrázku:

   ![Obrázek mapy znázorňující vykreslený výsledek](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps poskytuje dva různé způsoby nastavení jazyka a regionálního zobrazení pro vykreslenou mapu. První možností je přidat tyto informace do globálního `atlas` oboru názvů, což způsobí, že se všechny instance mapového ovládacího prvku ve vaší aplikaci budou ve výchozím nastavení nacházet. Následující nastaví jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Druhá možnost je předat tyto informace do možností mapy při načítání mapy, jako je tato:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Díky webové sadě SDK je možné načíst více instancí mapy na stejné stránce s různými nastaveními jazyka a oblasti. Kromě toho lze tato nastavení aktualizovat po načtení mapy pomocí `setStyle` funkce mapy. 

Tady je příklad Azure Maps s jazykem, který je nastaven na "fr-FR" a v místním zobrazení na hodnotu "auto".

![Obrázek mapy znázorňující popisky ve francouzštině](./media/how-to-use-map-control/websdk-localization.png)

Úplný seznam podporovaných jazyků a regionálních zobrazení je popsán [zde](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Podpora cloudu Azure Government

Sada Azure Maps Web SDK podporuje cloud Azure Government. Všechny adresy URL JavaScriptu a CSS použité pro přístup k sadě Azure Maps Web SDK zůstávají stejné. Aby bylo možné se připojit ke cloudové verzi Azure Government Azure Maps platformy, bude nutné provést následující úlohy.

Při použití interaktivního ovládacího prvku mapy přidejte následující řádek kódu před vytvořením instance `Map` třídy. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Při ověřování map a služeb nezapomeňte použít podrobnosti ověřování Azure Maps z Azure Government cloudové platformy.

Při použití modulu služeb musí být při vytváření instance koncového bodu adresy URL rozhraní API nastavena doména pro služby. Například následující kód vytvoří instanci `SearchURL` třídy a odkazuje na doménu na cloud Azure Government.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Pokud máte přímý přístup ke službě Azure Maps REST, změňte doménu adresy URL `atlas.azure.us`na. Pokud například používáte službu rozhraní API pro hledání, změňte doménu adresy URL z `https://atlas.microsoft.com/search/` na. `https://atlas.azure.us/search/`

## <a name="next-steps"></a>Další kroky

Naučte se vytvořit mapu a pracovat s ní:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

Naučte se styl mapy:

> [!div class="nextstepaction"]
> [Výběr stylu mapy](choose-map-style.md)

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
