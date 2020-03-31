---
title: Zobrazit výsledky hledání na mapě | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak provést požadavek na vyhledávání pomocí sady Microsoft Azure Maps Web SDK a zobrazit výsledky na mapě.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e82a1daee381c7bad19c83fa735d0028bef2010e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371401"
---
# <a name="show-search-results-on-the-map"></a>Zobrazit výsledky hledání na mapě

V tomto článku se můžete dotazovat, jak vyhledat místo, kde se zájem bude zajímat, a zobrazit výsledky vyhledávání na mapě.

Existují dva způsoby, jak vyhledat místo zájmu. Jedním ze způsobů je použití modulu služby k vytvoření požadavku na vyhledávání. Druhou možnosti je vytvořit požadavek na vyhledávání v [rozhraní Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) prostřednictvím rozhraní Fetch [API](https://fetch.spec.whatwg.org/). Oba způsoby jsou popsány níže.

## <a name="make-a-search-request-via-service-module"></a>Vytvoření požadavku na vyhledávání prostřednictvím servisního modulu

<iframe height='500' scrolling='no' title='Zobrazit výsledky hledání na mapě (Servisní modul)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se na výsledky vyhledávání pero zobrazit na<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapě <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>(Service Module)</a> podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok vytvoří objekt mapy a nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu vytvoří `TokenCredential` k ověření http požadavků na Azure Maps s přístupovým tokenem. Potom předá `TokenCredential` `atlas.service.MapsURL.newPipeline()` a vytvoří [pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) instance. Představuje `searchURL` adresu URL pro operace Azure Maps [Search.](https://docs.microsoft.com/rest/api/maps/search)

Třetí blok kódu vytvoří objekt zdroje dat pomocí třídy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) a přidá do ní výsledky hledání. [Vrstva symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony k vykreslení dat založených na bodu zabalených do [Zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbolů na mapě.  Poté se vytvoří vrstva symbolu. Zdroj dat se přidá do vrstvy symbolů, která je pak přidána do mapy.

Čtvrtý blok kódu používá [metodu SearchFuzzy](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) v [modulu služby](how-to-use-services-module.md). To vám umožní provádět vyhledávání textu z volného formuláře přes [Get Search Fuzzy zbytek API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro vyhledávání bodů zájmu. Získat požadavky na hledání fuzzy rozhraní API může zpracovat libovolnou kombinaci fuzzy vstupů. Kolekce funkcí GeoJSON z odpovědi je pak `geojson.getFeatures()` extrahována pomocí metody a přidána do zdroje dat, což automaticky vede k vykreslení dat na mapě prostřednictvím vrstvy symbolů.

Poslední blok kódu upraví hranice kamery pro mapu pomocí vlastnosti [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) mapy.

Požadavek na hledání, zdroj dat, vrstva symbolů a hranice kamery jsou uvnitř [posluchače události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy. Chceme zajistit, aby se výsledky zobrazovaly po úplném načtení mapy.


## <a name="make-a-search-request-via-fetch-api"></a>Vytvoření žádosti o hledání pomocí rozhraní Fetch API

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Podívejte se <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>na výsledky vyhledávání</a> pero<a href='https://codepen.io/azuremaps'>@azuremaps</a>zobrazit na mapě podle Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt mapy. Nastaví mechanismus ověřování pro použití přístupového tokenu. Můžete vidět [vytvořit mapu](./map-create.md) pro pokyny.

Druhý blok kódu vytvoří adresu URL, na kterou chcete požádat o hledání. Vytvoří také dvě pole pro uložení hranic a kolíků pro výsledky hledání.

Třetí blok kódu používá [rozhraní API pro načtení](https://fetch.spec.whatwg.org/). Rozhraní [Api pro načtení](https://fetch.spec.whatwg.org/) se používá k vytvoření požadavku na [rozhraní Azure Maps Fuzzy search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) k vyhledání bodů zájmu. Rozhraní API pro přibližné vyhledávání zvládne libovolnou kombinaci přibližných vstupů. Potom zmanipuluje a analyzuje odpověď hledání a přidá argumenty výsledků do pole searchPins.

Čtvrtý blok kódu vytvoří objekt zdroje dat pomocí třídy [DataSource.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) V kódu přidáme výsledky hledání do zdrojového objektu. [Vrstva symbolů](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá text nebo ikony k vykreslení dat založených na bodu zabalených do [Zdroje dat](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbolů na mapě. Poté se vytvoří vrstva symbolu. Zdroj dat se přidá do vrstvy symbolů, která je pak přidána do mapy.

Poslední blok kódu vytvoří objekt [BoundingBox.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) Používá pole výsledků, a pak se upraví hranice kamery pro mapu pomocí [mapy setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Potom vykreslí výsledné kolíky.

Požadavek na hledání, zdroj dat, vrstva symbolů a hranice kamery jsou nastaveny v rámci [posluchače události](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy, aby bylo zajištěno, že výsledky se zobrazí po úplném načtení mapy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

Další informace o **fuzzy vyhledávání**:

> [!div class="nextstepaction"]
> [Rozhraní API pro přibližné vyhledávání v Mapách Azure](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Úplné příklady kódu naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [Zobrazení pokynů pro trasu z A do B](map-route.md)
