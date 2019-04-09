---
title: Zobrazení výsledků hledání s Azure Maps | Dokumentace Microsoftu
description: Jak provést žádost o vyhledávání pomocí map Azure a zobrazení výsledků na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: be01c9d96386804b8bc074d81041104cbf592df6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271590"
---
# <a name="show-search-results-on-the-map"></a>Na mapě zobrazit výsledky hledání

Tento článek popisuje, jak na mapě zobrazit výsledky hledání a vyhledejte umístění, které vás zajímají.

Existují dva způsoby, jak vyhledat umístění, které vás zajímají. Jedním ze způsobů je modul služby můžete vytvořit žádost o vyhledávání. Druhý způsob je vytvořit žádost o vyhledávání [rozhraní API pro Azure Maps přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) prostřednictvím [Fetch API](https://fetch.spec.whatwg.org/). Obě možnosti jsou popsány níže.

## <a name="make-a-search-request-via-service-module"></a>Vytvořit žádost o vyhledávání prostřednictvím modulu service

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání v mapě (modulu Service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>zobrazit výsledky hledání na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a nastaví mechanismus ověřování využívat klíč předplatného. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Vytvoří druhý blok kódu `SubscriptionKeyCredentialPolicy` k ověření požadavků HTTP ve službě Azure Maps se klíč předplatného. Pak bude `atlas.service.MapsURL.newPipeline()` přijímá `SubscriptionKeyCredential` zásady a vytvoří [kanálu](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest) instance. `searchURL` Představuje adresu URL ke službě Azure Maps [hledání](https://docs.microsoft.com/rest/api/maps/search) operace.

Třetí bloku kódu vytvoří objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídu a přidejte do ní výsledky hledání. A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  Symbol vrstvy se pak vytvoří a bude přidán zdroj dat do vrstvy symbol, který se pak přidá do mapy.

Čtvrtý blok kódu používá [SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) metodu [modulu service](https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js). To umožňuje provádět textové vyhledávání volnou formou pomocí [získat vyhledávání přibližných shod rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Rozhraní API pro vyhledávání přibližných shod Get může zpracovat libovolnou kombinaci přibližných shod vstupů. Kolekce funkcí GeoJSON z odpovědi se pak extrahuje pomocí `geojson.getFeatures()` – metoda a přidán do zdroje dat, výsledkem je automaticky dat, vykreslované na mapě prostřednictvím vrstev symbol.

Poslední blok kódu nastaví fotoaparátu mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) vlastnost.

Hledání požádat o zdroji dat a vrstvy symbol a hranice fotoaparát jsou vytvořeny a sady v rámci mapy je připraven [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit, že výsledky se zobrazí po načtení mapy plně.


## <a name="make-a-search-request-via-fetch-api"></a>Vytvořit žádost o vyhledávání přes rozhraní Fetch API

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>zobrazit výsledky na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a nastaví mechanismus ověřování využívat klíč předplatného. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu se vytvoří adresa URL na vytvořit žádost o vyhledávání. Vytvoří také dvě pole pro uložení hranice a PIN kódů pro výsledky hledání.

Třetí blok kódu používá [Fetch API](https://fetch.spec.whatwg.org/) na vytvořit žádost o [rozhraní API pro Azure Maps přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Rozhraní API pro vyhledávání přibližných shod dokáže zpracovat libovolnou kombinaci přibližných shod vstupů. Poté zpracuje a analyzuje reakce na vyhledávání a přidá pole searchPins PIN kódy výsledků.

Čtvrtý bloku kódu vytvoří objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídu a přidejte do ní výsledky hledání. A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Symbol vrstvy se pak vytvoří a bude přidán zdroj dat do vrstvy symbol, který se pak přidá do mapy.

Vytvoří poslední blok kódu [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) pomocí pole výsledky a pak nastaví fotoaparátu mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Pak vykreslí PIN kódy výsledků.

Požadavek hledání, zdroji dat a vrstvy symbol a hranice fotoaparátu se nastavují v rámci mapy [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit, že výsledky se zobrazí po načtení mapy plně.

## <a name="next-steps"></a>Další postup

Další informace o **vyhledávání přibližných shod**:

> [!div class="nextstepaction"]
> [Azure Maps vyhledávání přibližných shod rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Naleznete v následujících článcích pro úplné příklady:

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](./map-route.md)
