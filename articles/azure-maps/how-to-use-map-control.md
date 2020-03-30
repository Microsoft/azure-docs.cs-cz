---
title: Začínáme s ovládacím prvkem webové mapy | Mapy Microsoft Azure
description: Přečtěte si, jak pomocí knihovny JavaScriptu na straně klienta map Microsoft Azure Maps vykreslit mapy a vložené funkce Azure Maps do vaší webové nebo mobilní aplikace.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335237"
---
# <a name="use-the-azure-maps-map-control"></a>Použití ovládacího prvku mapy Azure

Knihovna JavaScriptu na straně klienta map umožňuje vykreslit mapy a vložené funkce Azure Maps do webové nebo mobilní aplikace.

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapu můžete vložit na webovou stránku pomocí knihovny JavaScriptu na straně klienta.

1. Vytvořte nový soubor HTML.

2. Načtěte v azure maps web sdk. Můžete zvolit jednu ze dvou možností;

    * Pomocí globálně hostované verze cdn sady Azure Maps Web SDK přidáním odkazů na `<head>` JavaScript a šablonu stylů v elementu souboru HTML:

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * Načtěte zdrojový kód Azure Maps Web SDK místně pomocí balíčku NPM [s ovládacím prvkem azure-maps-control](https://www.npmjs.com/package/azure-maps-control) a hostujte ho pomocí aplikace. Tento balíček také obsahuje definice jazyka TypeScript.

        > **npm nainstalovat azure-maps-control**

       Pak přidejte odkazy na šablonu stylů Azure Maps `<head>` a odkazy na zdroj skriptu na prvek souboru:

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > Definice psacího stroje lze importovat do aplikace přidáním následujícího kódu:
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Chcete-li vykreslit mapu tak, aby vyplnila celé `<style>` tělo `<head>` stránky, přidejte do prvku následující prvek.

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

4. V textu stránky přidejte `<div>` prvek a dejte `id` mu **myMap**.

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. Chcete-li inicializovat ovládací prvek mapy, definujte novou značku skriptu v textu html. Předat v `id` mapě `<div>` nebo `HTMLElement` (například) `document.getElementById('myMap')`jako první parametr při vytváření `Map` instance třídy. K ověření mapy pomocí [možností ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)použijte vlastní klíč účtu Azure Maps nebo přihlašovací údaje služby Azure Active Directory (AAD). 

   Pokud potřebujete vytvořit účet nebo najít klíč, postupujte podle pokynů v [tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a získání [primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . 

   Možnost **jazyka** určuje jazyk, který má být použit pro popisky mapy a ovládací prvky. Další informace o podporovaných jazycích naleznete v [tématu Podporované jazyky](supported-languages.md). Pokud používáte klíč předplatného pro ověřování, použijte následující:

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

   Pokud používáte azure active directory (AAD) pro ověřování, použijte následující:

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

   Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps najdete [zde](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples). 
    
   Další informace najdete v dokumentu [Ověřování pomocí Map Azure](azure-maps-authentication.md) a taky [ukázky ověřování Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

6. Volitelně můžete najít přidání následujících prvků metaznačky do hlavy stránky užitečné:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Dát to všechno dohromady soubor HTML by měl vypadat něco jako následující kód:

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

8. Otevřete soubor ve webovém prohlížeči a zobrazte vykreslenou mapu. Mělo by to vypadat jako obrázek níže:

   ![Obrázek mapy znázorňující vykreslený výsledek](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps poskytuje dva různé způsoby nastavení jazyka a místní zobrazení pro vykreslené mapy. První možností je přidat tyto informace `atlas` do globálního oboru názvů, což bude mít za následek všechny instance ovládacího prvku mapy v aplikaci výchozí pro tato nastavení. Následující nastaví jazyk na francouzštinu ("fr-FR") a regionální zobrazení "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Druhou možností je předat tyto informace do možností mapy při načítání mapy takto:

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
> Pomocí sady Web SDK je možné načíst více instancí mapy na stejné stránce s různým nastavením jazyka a oblasti. Kromě toho mohou být tato nastavení aktualizována `setStyle` po načtení mapy pomocí funkce mapy. 

Tady je příklad Azure Maps s jazykem nastaveným na "fr-FR" a místní zobrazení nastavené na "Auto".

![Obrázek mapy znázorňující popisky ve francouzštině](./media/how-to-use-map-control/websdk-localization.png)

Kompletní seznam podporovaných jazyků a regionálních zobrazení je zdokumentován [zde](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Cloudová podpora Azure Government

Webová sada Azure Maps Web SDK podporuje cloud Azure Government. Všechny adresy URL JavaScriptu a CSS používané pro přístup k webové sdk Azure Maps zůstanou stejné. Pro připojení k cloudové verzi Azure Government na platformě Azure Maps se budou muset udělat následující úkoly.

Při použití interaktivního ovládacího prvku mapy přidejte před `Map` vytvořením instance třídy následující řádek kódu. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Nezapomeňte při ověřování mapy Azure používat podrobnosti o ověřování z cloudové platformy Azure Government při ověřování mapy a služeb.

Při použití modulu služby, doménu pro služby je třeba nastavit při vytváření instance koncového bodu URL rozhraní API. Například následující kód vytvoří instanci `SearchURL` třídy a odkazuje doménu na cloud Azure Government.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Pokud máte přímý přístup ke službám Azure `atlas.azure.us`Maps REST, změňte doménu URL na . Pokud například používáte službu rozhraní API `https://atlas.microsoft.com/search/` pro `https://atlas.azure.us/search/`vyhledávání, změňte doménu adresy URL z na .

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak vytvořit mapu a pracovat s ní:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

Přečtěte si, jak vytvořit styl mapy:

> [!div class="nextstepaction"]
> [Výběr stylu mapy](choose-map-style.md)

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)

Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps, najdete v tématu:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
