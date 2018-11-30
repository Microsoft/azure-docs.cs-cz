---
title: Mapování funkce styl ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o Azure Maps styl související funkce.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c77467efbbc1c33be5bc2aaf3251a5803beee864
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52317105"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Vyberte styl map ve službě Azure Maps

Azure Maps má čtyři různé mapy styly lze vybírat. Další informace o mapování stylů, najdete v článku [podporován styly mapy ve službě Azure Maps](./supported-map-styles.md). Tento článek ukazuje, jak můžete nastavit styl na mapě zatížení, nastavit nový styl a pomocí ovládacího prvku pro výběr stylu funkce související se stylem.

## <a name="set-style-on-map-load"></a>Styl sady zátěž mapy

<iframe height='500' scrolling='no' title='Nastavení stylu na mapě zatížení' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>nastavení stylu na mapě zatížení</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Blok kódu výše nastaví klíč předplatného a vytvoří objekt Map stylu nastavena na grayscale_dark. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

## <a name="update-the-style"></a>Aktualizace stylu

<iframe height='500' scrolling='no' title='Aktualizuje se styl' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizace styl</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Blok kódu výše nastaví klíč předplatného a vytvoří objekt Map bez předem nastavení stylu. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu pomocí mapy [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) metody nastavte styl mapy satelit.

## <a name="add-the-style-picker"></a>Přidat nástroj pro výběr stylu

<iframe height='500' scrolling='no' title='Přidání nástroje pro výběr stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>přidání nástroje pro výběr stylu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu nastaví klíč předplatného a vytvoří objekt Map, grayscale_dark předem je nastaven styl mapy. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu vytvoří selektor stylů pomocí atlas [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) konstruktoru.

Styl ovládacího prvku pro výběr umožňuje výběr stylu pro mapu. Třetí blok kódu do mapy pomocí mapy Přidá výběr stylu [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody. Selektor stylů je v rámci mapy **naslouchací proces událostí** zajistit, že načte po plně načte mapy.

## <a name="next-steps"></a>Další postup

Další informace o třídách a metodách použité v tomto článku:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Přidejte ovládací prvek pro vaše mapy:

> [!div class="nextstepaction"]
> [Přidat ovládací prvky mapy](./map-add-controls.md)

Přidání špendlíku mapy:

> [!div class="nextstepaction"]
> [Přidání špendlíku](./map-add-pin.md)
