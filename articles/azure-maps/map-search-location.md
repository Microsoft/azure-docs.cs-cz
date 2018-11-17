---
title: Zobrazení výsledků hledání s Azure Maps | Dokumentace Microsoftu
description: Jak provést žádost o vyhledávání pomocí map Azure a zobrazení výsledků na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 031085b3048d0ffc92dd5a35b4054903088b4858
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824332"
---
# <a name="show-search-results-on-the-map"></a>Na mapě zobrazit výsledky hledání

Tento článek popisuje, jak na mapě zobrazit výsledky hledání a vyhledejte umístění, které vás zajímají.

Existují dva způsoby, jak vyhledat umístění, které vás zajímají. Jedním ze způsobů je modul služby můžete vytvořit žádost o vyhledávání. Druhý způsob je, aby žádost o vyhledávání prostřednictvím [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní API pro Azure Maps přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Obě možnosti jsou popsány níže.

## <a name="make-a-search-request-via-service-module"></a>Vytvořit žádost o vyhledávání prostřednictvím modulu service

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání v mapě (modulu Service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>zobrazit výsledky hledání na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a inicializuje službu klienta. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý blok kódu používá [getSearchFuzzy](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchfuzzy-string--searchgetsearchfuzzyoptionalparams-) metodu [modulu service](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1). To umožňuje provádět textové vyhledávání volnou formou pomocí [vyhledávání přibližných shod rest API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Vyhledávání přibližných shod rozhraní API mohou zpracovat libovolnou kombinaci přibližných shod vstupů. Odpověď od služby vyhledávání přibližných shod se pak Parsuje do pomocí formátu GeoJSON [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metody. 

Třetí bloku kódu vytvoří objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídu a přidejte do ní výsledky hledání. A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě.  Symbol vrstvy se pak vytvoří a bude přidán zdroj dat do vrstvy symbol, který se pak přidá do mapy.

Poslední blok kódu nastaví fotoaparátu mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) vlastnost.

Požadavek hledání, zdroj dat a vrstvy symbolů a hranice fotoaparátu vytvářejí se a nastavená na mapě [naslouchací proces událostí](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) zajistit, že výsledky se zobrazí po načtení mapy plně.


## <a name="make-a-search-request-via-xmlhttprequest"></a>Vytvořit žádost o vyhledávání prostřednictvím XMLHttpRequest

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>zobrazit výsledky na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Odešle druhou bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní API pro Azure Maps přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Rozhraní API pro vyhledávání přibližných shod dokáže zpracovat libovolnou kombinaci přibližných shod vstupů. 

Třetí bloku kódu analyzuje hledat odpovědi a výsledky ukládá do pole pro výpočet hranice. Potom vrátí výsledky hledání.

Čtvrtý bloku kódu vytvoří objekt zdroje dat pomocí [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) třídu a přidejte do ní výsledky hledání. A [symbol vrstvy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) používá k vykreslení dat na základě bodu zabalené v textu nebo ikony [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symboly na mapě. Symbol vrstvy se pak vytvoří a bude přidán zdroj dat do vrstvy symbol, který se pak přidá do mapy.

Vytvoří poslední blok kódu [BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) pomocí pole výsledky a pak nastaví fotoaparátu mezí mapy pomocí mapy [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-). Potom vykreslit výsledek kódy PIN.

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
