---
title: Zobrazení výsledků hledání s Azure Maps | Dokumentace Microsoftu
description: Jak provést žádost o vyhledávání pomocí map Azure a zobrazení výsledků na mapě jazyka JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b0ab271eab45a6f4b05d01713e2e2ddd22a22ea3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746599"
---
# <a name="show-search-results-on-the-map"></a>Na mapě zobrazit výsledky hledání

Tento článek ukazuje, jak vytvořit žádost o vyhledávání a zobrazení výsledků hledání na mapě. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='500' scrolling='no' title='Zobrazení výsledků hledání na mapě' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>zobrazit výsledky na mapě</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří a přidá vrstvu služby search se vážou na mapě. Můžete zobrazit [přidání špendlíku na mapě](./map-add-pin.md) pokyny.

Odešle třetí bloku kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [mapy přibližných shod API služby Azure Search](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi shromáždí informace o zeměpisné šířce a délce pro každou vrácenou polohu. Přidá všechny bodů polohy do mapy jako kódy PIN a upravuje rozsah mapy vykreslit všechny kódy PIN.


## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Azure Maps vyhledávání přibližných shod rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích: 
* [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)
* [Zobrazení pokynů pro trasu z A do B](./map-route.md)
