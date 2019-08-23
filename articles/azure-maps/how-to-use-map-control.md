---
title: Začínáme s ovládacím prvkem webového mapování v Azure Maps | Microsoft Docs
description: Naučte se, jak používat mapu Azure Maps k řízení knihovny JavaScriptu na straně klienta.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fa6a3af7893830eba2f4a5c43184991bff68d8a8
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898210"
---
# <a name="use-the-azure-maps-map-control"></a>Použití ovládacího prvku Azure Maps mapa

Ovládací prvek Mapa knihovna JavaScriptu na straně klienta umožňuje vykreslovat mapy a vložené Azure Maps funkce do vaší webové nebo mobilní aplikace.

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapu můžete vložit do webové stránky pomocí Ovládací prvek Mapa knihovny JavaScript na straně klienta.

1. Vytvořte nový soubor HTML.

2. Načtěte v sadě Azure Maps Web SDK. To lze provést pomocí jedné ze dvou možností:

    a. Pomocí globálně hostované verze CDN rozhraní Azure Maps Web SDK přidejte koncové body adresy URL do šablony stylů a odkazy na skripty v `<head>` elementu souboru:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Případně můžete zdrojový kód Azure Maps Web SDK načíst místně pomocí balíčku [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm a hostovat ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript.

    > NPM instalace Azure-Maps-Control

    Pak přidejte odkazy na Azure Maps šablon stylů a odkazů na zdroj skriptu k `<head>` elementu souboru:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Chcete-li vykreslit mapu, aby vyplnila celé tělo stránky, přidejte `<style>` `<head>` do elementu následující element.

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

4. V těle stránky přidejte `<div>` prvek a poskytněte `id` mu **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Chcete-li inicializovat mapový ovládací prvek, definujte nový oddíl v těle HTML a vytvořte skript. Při vytváření instance `id` `Map` třídy předejte `<div>` v mapě `HTMLElement` nebo (například `document.getElementById('myMap')`) jako první parametr. Použijte vlastní klíč účtu Azure Maps nebo přihlašovací údaje pro Azure Active Directory (AAD) k ověření mapování pomocí [možností ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions). Pokud potřebujete vytvořit účet nebo najít klíč, přečtěte si téma [Správa účtu Azure Maps a klíčů](how-to-manage-account-keys.md). Možnost **Language** určuje jazyk, který má být použit pro popisky a ovládací prvky mapy. Další informace o podporovaných jazycích najdete v tématu [podporované jazyky](supported-languages.md). Pokud používáte klíč předplatného pro ověřování.

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

    Pokud používáte Azure Active Directory (AAD) pro ověřování:

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

    Další informace najdete v dokumentu [ověřování pomocí Azure Maps](azure-maps-authentication.md) .

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

8. Otevřete soubor ve webovém prohlížeči a zobrazte vykreslenou mapu. Měl by vypadat jako v následujícím kódu:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Jak používat mapový ovládací prvek" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Podívejte se na pero, <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>jak použít mapový ovládací prvek</a> pomocí<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps poskytuje dva různé způsoby nastavení jazyka a regionálního zobrazení mapy. První možností je přidat tyto informace do globálního `atlas` oboru názvů, což způsobí, že se všechny instance mapového ovládacího prvku ve vaší aplikaci budou ve výchozím nastavení nacházet. Následující nastaví jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

Druhá možnost je předat tyto informace do možností mapy při načítání mapy jako:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

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

## <a name="next-steps"></a>Další postup

Naučte se vytvořit mapu a pracovat s ní:

> [!div class="nextstepaction"]
> [Vytvořit mapu](map-create.md)

Naučte se styl mapy:

> [!div class="nextstepaction"]
> [Zvolit styl mapy](choose-map-style.md)

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Vytvořit mapu](map-create.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)