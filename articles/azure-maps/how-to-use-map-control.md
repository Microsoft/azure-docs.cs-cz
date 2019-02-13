---
title: Jak používat mapový ovládací prvek Azure Maps | Dokumentace Microsoftu
description: Zjistěte, jak použít knihovnu Javascript na straně klienta Azure Maps mapového ovládacího prvku.
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 877393de20788b0aa1c76084b121a82f12715cd3
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118067"
---
# <a name="use-the-azure-maps-map-control"></a>Použití mapového ovládacího prvku Azure Maps

Knihovna Javascript na straně klienta mapový ovládací prvek umožňuje vykreslit mapy a vložené funkce Azure Maps do vašich webových nebo mobilních aplikací.

## <a name="create-a-new-map-in-a-web-page"></a>Vytvoření nové mapy na webové stránce

Mapě můžete vložit na webové stránce pomocí knihovny Javascript na straně klienta mapový ovládací prvek.

1. Vytvořte nový soubor HTML.

2. Načíst na webu Azure Maps SDK. To lze provést jedním ze dvou možností;
    
    a. Použít globálně hostované CDN verzi sady SDK Azure Maps webové tak, že přidáte koncové body adres URL s odkazy na šablony stylů a skripty v `<head>` prvek souboru:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. Můžete také načíst místně pomocí sady SDK webové mapy Azure zdrojový kód [řízení azure maps](https://www.npmjs.com/package/azure-maps-control) NPM balíček a hostujte ho s vaší aplikací. Tento balíček obsahuje také definice TypeScript.

    > npm install azure map – ovládací prvek

    Pak přidejte odkazy na Azure Maps šablony stylů a skripty zdroj odkazy `<head>` prvek souboru:

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. Pokud chcete zobrazit na mapě, tak, že vyplní celé těla stránky, přidejte následující `<style>` elementu `<head>` elementu.

    ```html
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

4. V těle stránky, přidejte `<div>` elementu a přiřaďte mu `id` z **myMap**. 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Inicializace mapový ovládací prvek, definujte nový oddíl v textu html a vytvořit skript. Použít vlastní klíč účtu Azure Maps nebo přihlašovací údaje Azure Active Directory (AAD) k ověřování pomocí mapování [možnosti ověřování](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.authenticationoptions). Pokud potřebujete k vytvoření účtu služby nebo najít vaše klíče najdete v tématu [jak spravovat účet Azure Maps a klíče](how-to-manage-account-keys.md). **Jazyk** Určuje jazyk, který chcete použít pro popisky mapy a ovládací prvky. Další informace o podporovaných jazyků najdete v tématu [podporované jazyky](supported-languages.md). Pokud používáte klíč předplatného pro ověřování.

    ```html
    <script type='text/javascript'>
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

    Pokud se používá pro ověřování Azure Active Directory (AAD):

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

6. Volitelně můžete zjistit přidávání na hlavní stránce užitečné následující prvky značku meta:

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. Vložení všechno dohromady soubor HTML by měl vypadat přibližně takto:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
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
        
        <script type='text/javascript'>
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

8. Otevřete soubor ve webovém prohlížeči a zobrazení vykreslené mapy. By měl vypadat nějak takto:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Jak používat mapový ovládací prvek" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobrazit pera <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>jak používat mapový ovládací prvek</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvořit a interakce s mapou:

> [!div class="nextstepaction"]
> [Vytvoření mapy](map-create.md)

Další informace o stylu mapy:

> [!div class="nextstepaction"]
> [Vyberte styl mapy](choose-map-style.md)
