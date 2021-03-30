---
title: Zobrazení provozu v reálném čase na Azure Maps Power BI vizuálů | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit data v reálném čase na vizuálu Microsoft Azure Maps pro Power BI.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: fe9e86f3c1d155a1f0b29f500de3954e360b4aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261720"
---
# <a name="show-real-time-traffic"></a>Zobrazení provozu v reálném čase

Funkce vrstvy přenosu překrývá data o přenosech dat v reálném čase nad mapou. Chcete-li povolit tuto funkci, přesuňte posuvník **vrstvy přenosů** v podokně **Formát** do polohy **on** . Tato akce překryje data toku přenosu jako barevně kódovaných cest.

> [!div class="mx-imgBorder"]
> ![Mapa zobrazující data o provozu v reálném čase](media/power-bi-visual/traffic-layer.png)

V části **přenosová vrstva** jsou k dispozici následující nastavení.

| Nastavení         | Popis    |
|-----------------|----------------|
| Zobrazit incidenty  | Určuje, jestli se na mapě mají zobrazovat incidenty přenosu dat, například uzavření a vytváření provozu. |
| Řízení provozu | Přidá na mapu tlačítko, které umožňuje čtenářům sestav zapnout nebo vypnout vrstvu provozu.  |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Maps Power BI vizuálu:

> [!div class="nextstepaction"]
> [Principy vrstev v Azure Maps Power BI vizuálu](power-bi-visual-understanding-layers.md)

> [!div class="nextstepaction"]
> [Správa Azure Mapsového vizuálu v rámci vaší organizace](power-bi-visual-manage-access.md)
