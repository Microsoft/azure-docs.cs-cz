---
title: Zobrazit výsledky hledání pomocí map Azure | Microsoft Docs
description: Jak provést žádost o vyhledávání s Azure mapy a zobrazit výsledky na mapě Javascrip
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>Výsledky hledání zobrazit na mapě

Tento článek ukazuje, jak provést žádost o vyhledávání a zobrazit výsledky vyhledávání na mapě. 

## <a name="understand-the-code"></a>Pochopení kódu

<iframe height='500' scrolling='no' title='Zobrazit výsledky vyhledávání na mapu' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>výsledky hledání zobrazit na mapě</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu vytvoří a přidá vrstvu vyhledávání PIN kódy na mapě. Můžete zobrazit [přidat pin na mapě](./map-add-pin.md) pokyny.

Odešle třetí blok kódu [XMLHttpRequest](https://xhr.spec.whatwg.org/) k [Azure přibližné vyhledávání rozhraní API map](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Poslední blok kódu analyzuje příchozí odpovědi. Pro úspěšné odpovědi sbírá informace zeměpisné šířky a délky pro každé umístění vrátila. Přidá všechny body umístění mapy jako kódy PIN a upraví mezí mapy k vykreslení všechny kódy PIN.


## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 

* [Azure mapuje přibližné vyhledávání rozhraní API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
