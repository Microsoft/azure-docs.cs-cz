---
title: Zobrazení výsledků hledání s Azure Maps | Dokumentace Microsoftu
description: Jak provést žádost o vyhledávání pomocí map Azure a zobrazení výsledků na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b324d0a68fde8f47072a087330f2e40a99378984
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299470"
---
# <a name="show-search-results-on-the-map"></a>Na mapě zobrazit výsledky hledání

Tento článek popisuje, jak na mapě zobrazit výsledky hledání a vyhledejte umístění, které vás zajímají. 

Existují dva způsoby, jak vyhledejte umístění, které vás zajímají, jedním ze způsobů, je pomocí modulu service vytvoříte žádost o vyhledávání a druhý je tím, že žádost o vyhledávání prostřednictvím [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní APIAzureMapspřibližnévyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy). Můžeme probírat i níže.

## <a name="making-a-search-request-via-service-module"></a>Provedení požadavku hledání prostřednictvím modulu service

### <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání v mapě (modulu Service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>zobrazit výsledky hledání na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a vytvoří instanci služby klienta. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý blok kódu používá vyhledávání přibližných shod [mapy přibližných shod API služby Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Vyhledávání přibližných shod rozhraní API mohou zpracovat libovolnou kombinaci přibližných shod vstupů. Odpověď od služby vyhledávání přibližných shod se pak Parsuje do pomocí formátu GeoJSON [getGeoJsonSearchResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonsearchresponse?view=azure-iot-typescript-latest#geojsonsearchresponse) metody. Zadané kódy PIN se poté přidají ke mapu, aby zobrazovala body zájmu na mapě.

Poslední blok kódu nastaví fotoaparátu mezí mapy pomocí mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) vlastnost.


##  <a name="making-a-search-request-via-xmlhttprequest"></a>Provedení požadavku hledání prostřednictvím XMLHttpRequest

### <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>zobrazit výsledky na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý blok kódu přidává vrstvu výsledky hledání do mapy. Vrstva výsledky hledání se zobrazí výsledky hledání podle vážou na mapě.

Odešle třetí bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [rozhraní API pro Azure Maps přibližné vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Vyhledávání přibližných shod rozhraní API mohou zpracovat libovolnou kombinaci přibližných shod vstupů.

Analyzuje odpovědi poslední blok kódu a upravuje nastaví fotoaparátu mezí mapy pomocí mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) k vykreslení PIN kódy výsledků.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Azure Maps vyhledávání přibližných shod rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)
* [Zobrazení pokynů pro trasu z A do B](./map-route.md)
