---
title: Přidat vlastního html ve službě Azure Maps | Dokumentace Microsoftu
description: Jak přidat vlastního html k mapě jazyka Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e5cfbc7ddc10edf9b21afce73e3b7f8795fcdac9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121953"
---
# <a name="add-custom-html-to-the-map"></a>Přidat vlastního HTML do mapy

Tento článek ukazuje, jak přidat vlastního HTML, jako je například soubor bitové kopie do mapy.

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='466' scrolling='no' title='Přidat vlastního html k mapě - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>přidat vlastního html k mapě - png</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt Map. Můžete zobrazit [Vytvořte mapu](./map-create.md) pokyny.

Druhý bloku kódu vytvoří HTML element z image.

Poslední blok kódu používá [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) funkce třídy map přidávání bitové kopie na určenou pozici na mapě.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Zobrazení výsledků hledání](./map-search-location.md)

> [!div class="nextstepaction"]
> [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)