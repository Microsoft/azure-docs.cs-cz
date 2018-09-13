---
title: Mapování funkce styl ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o Azure Maps styl související funkce.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 160752cd0467ef307f7a45b1e0d703c7ddd5d773
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720799"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Vyberte styl map ve službě Azure Maps
Azure Maps má čtyři různé mapy styly lze vybírat. Další informace o mapování stylů, najdete v článku [podporován styly mapy ve službě Azure Maps](./supported-map-styles.md). Tento článek ukazuje, jak můžete nastavit styl na mapě zatížení, nastavit nový styl a pomocí ovládacího prvku pro výběr stylu funkce související se stylem.

## <a name="setting-style-on-map-load"></a>Nastavení stylu na mapě zatížení

<iframe height='500' scrolling='no' title='Nastavení stylu na mapě zatížení' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>nastavení stylu na mapě zatížení</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Výše uvedený kód vytvoří objekt map stylem nastavit na stupně šedé. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

## <a name="updating-the-style"></a>Aktualizuje se styl

<iframe height='500' scrolling='no' title='Aktualizuje se styl' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizace styl</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt map bez předem nastavení stylu. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý hodiny kódu používá na mapě [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) metody nastavte styl mapy satelit.

## <a name="adding-the-style-picker"></a>Přidání nástroje pro výběr stylu

<iframe height='500' scrolling='no' title='Přidání nástroje pro výběr stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobrazit pera <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>přidání nástroje pro výběr stylu</a> pomocí Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

První blok kódu ve výše uvedeném kódu vytvoří objekt map bez předem nastavení stylu. Zobrazit [Vytvořte mapu](./map-create.md) pokyny o tom, jak vytvořit mapu.

Druhý blok kódu vytvoří selektor stylů pomocí atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) konstruktoru.

Styl ovládacího prvku pro výběr umožňuje výběr stylu pro mapu. Třetí blok kódu do mapy pomocí mapy Přidá výběr stylu [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

## <a name="next-steps"></a>Další postup

Další informace o třídy a metody používané v tomto článku: 
* [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Další příklady kódu pro přidání do vaše mapy najdete v následujících článcích:
* [Přidat ovládací prvky mapy](./map-add-controls.md)
* [Přidání špendlíku](./map-add-pin.md)
