---
title: Přidat vlastní html v rámci služby Azure Maps | Microsoft Docs
description: Jak přidat vlastní html na mapu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600217"
---
# <a name="add-custom-html-to-the-map"></a>Přidat vlastní HTML do mapy

Tento článek ukazuje, jak přidat vlastní HTML, jako je soubor bitové kopie do mapy. 

## <a name="understand-the-code"></a>Vysvětlení kódu

<iframe height='466' scrolling='no' title='Přidat vlastní html na mapu - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>V tématu pera <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>přidat vlastní html na mapu - png</a> pomocí Azure mapy (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ve výše uvedeném kódu první blok kódu vytvoří objekt map. Můžete zobrazit [vytvoření mapy](./map-create.md) pokyny.

Druhý blok kódu vytvoří HTML element z bitové kopie.

Používá posledního bloku kódu [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) funkce třídy map přidat bitovou kopii na zadané pozici mapy.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
Další příklady kódu pro přidání do vaší mapy najdete v následujících článcích: 
* [Zobrazit výsledky hledání](./map-search-location.md)
* [Získání informací ze souřadnice](./map-get-information-from-coordinate.md)

