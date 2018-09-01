---
title: Zobrazení výsledků hledání s Azure Maps | Dokumentace Microsoftu
description: Jak provést žádost o vyhledávání pomocí map Azure a zobrazení výsledků na mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 31ff57f8a933ac17c6dfaa1a1fb0cf2bab0b6557
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345267"
---
# <a name="show-search-results-on-the-map"></a>Na mapě zobrazit výsledky hledání

Tento článek popisuje, jak na mapě zobrazit výsledky hledání a vyhledejte umístění, které vás zajímají. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání v mapě (modulu Service)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>zobrazit výsledky hledání na mapě (modulu Service)</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map a vytvoří instanci služby klienta. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý blok kódu používá vyhledávání přibližných shod [mapy přibližných shod API služby Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) pro hledání bodů zájmu. Vyhledávání přibližných shod rozhraní API mohou zpracovat libovolnou kombinaci přibližných shod vstupů. Odpověď od služby vyhledávání přibližných shod se pak Parsuje do formátu GeoJSON a PIN kódů se přidávají do mapy pro body zájmu na mapě zobrazit. 

Poslední blok kódu pomocí mapy přidá fotoaparátu mezí mapy [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) vlastnost.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Azure Maps vyhledávání přibližných shod rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)
* [Zobrazení pokynů pro trasu z A do B](./map-route.md)
