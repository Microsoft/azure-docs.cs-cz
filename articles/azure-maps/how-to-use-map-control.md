---
title: Začínáme s ovládacím prvkem webového mapování Microsoft Azure Maps
description: Zjistěte, jak přidat mapy k webovým a mobilním aplikacím pomocí Ovládací prvek Mapa knihovny JavaScript na straně klienta v Azure Maps. Podívejte se, jak lokalizovat mapy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 19db4fb0d75369a0f272eef1180e86f47d45d284
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335326"
---
# <a name="use-the-azure-maps-map-control"></a>Použití mapového ovládacího prvku Azure Maps

Ovládací prvek Mapa knihovna JavaScriptu na straně klienta umožňuje vykreslovat mapy a vložené Azure Maps funkce do vaší webové nebo mobilní aplikace.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít Ovládací prvek Mapa na webové stránce, musíte mít jeden z následujících požadavků:

* [Vytvořte účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account) a [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

* Získejte přihlašovací údaje pro Azure Active Directory (AAD) s [možnostmi ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapu můžete vložit do webové stránky pomocí Ovládací prvek Mapa knihovny JavaScript na straně klienta.

1. Vytvořte nový soubor HTML.

2. Načtěte v sadě Azure Maps Web SDK. Můžete zvolit jednu ze dvou možností:

    * Pomocí globálně hostované verze CDN sady Azure Maps Web SDK přidejte odkazy na JavaScript a šablonu stylů v `<head>` elementu souboru HTML:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Načtěte zdrojový kód Azure Maps Web SDK místně pomocí balíčku [Azure-Maps-Control](https://www.npmjs.com/package/azure-maps-control) npm a hostovat ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript.

      > **NPM instalace Azure-Maps-Control**

    Pak přidejte odkazy na Azure Maps šablonu stylů do `<head>` prvku souboru:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Definice TypeScript lze do aplikace importovat přidáním následujícího kódu:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Chcete-li vykreslit mapu, aby vyplnila celé tělo stránky, přidejte `<style>` do elementu následující element `<head>` .

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Nyní inicializujeme mapový ovládací prvek. Aby bylo možné ovládací prvek ověřit, budete muset vlastnit Azure Maps klíč předplatného nebo použít přihlašovací údaje Azure Active Directory (AAD) s [možnostmi ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Pokud používáte klíč předplatného pro ověřování, zkopírujte a vložte následující prvek skriptu do `<head>` prvku a pod první `<script>` prvek. Nahraďte `<Your Azure Maps Key>` klíčem primárního předplatného Azure Maps.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    Pokud pro ověřování používáte Azure Active Directory (AAD), zkopírujte a vložte následující prvek skriptu do `<head>` prvku a pod první `<script>` prvek.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
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
            }
        });
    </script>
   ```

    Další informace o ověřování pomocí Azure Maps naleznete v dokumentu [ověřování pomocí Azure Maps](azure-maps-authentication.md) . Seznam ukázek, které ukazují, jak integrovat Azure Active Directory (AAD) s Azure Maps, najdete [tady](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >V tomto příkladu jsme předali na `id` mapě `<div>` . Dalším způsobem, jak to provést, je předat do `HTMLElement` objektu předání `document.getElementById('myMap')` jako první parametr.

6. Volitelně může být užitečné přidat následující `meta` prvky do `head` prvku stránky:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. To všechno dohromady, váš soubor HTML by měl vypadat přibližně jako následující kód:

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


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

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
    <body onload="InitMap()">
        <div id="myMap"></div>
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

> [!NOTE]
> Je možné načíst více instancí mapy na stejné stránce s jiným nastavením jazyka a oblasti. Kromě toho lze tato nastavení aktualizovat po načtení mapy pomocí `setStyle` funkce mapy.

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

Pokud máte přímý přístup ke službě Azure Maps REST, změňte doménu adresy URL na `atlas.azure.us` . Pokud například používáte službu rozhraní API pro hledání, změňte doménu adresy URL z `https://atlas.microsoft.com/search/` na `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>Rozhraní JavaScript

Při vývoji pomocí JavaScriptu rozhraní může být užitečné jeden z následujících open-source projektů:

- [NG-Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -úhlová Obálka kolem Azure Maps.
- [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – komponenta Azure Maps Blazor.
- [Azure Maps reagující na komponentu](https://github.com/WiredSolutions/react-azure-maps) – reakce na reakci ovládacího prvku Azure Maps.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – komponenta Azure Maps pro aplikaci Vue.

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
