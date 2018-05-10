---
title: Přiblížení úrovně a dlaždici mřížky v rámci služby Azure Maps | Microsoft Docs
description: Další informace o úrovních přiblížení a dlaždici mřížky v rámci služby Azure Maps
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
ms.custom: ''
ms.openlocfilehash: 330333569f094fe3cec7f73ee3b20107ec70f5b5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Úrovně přiblížení a mřížka dlaždic
Azure mapy použít souřadnicový systém projekci Mercator kulovým (EPSG: 3857).

Na světě. je rozdělené do odmocnina dlaždice. Vykreslení (rastrové) má 19 úrovně přiblížení číslované 0 až 18. Vykreslení (Vector) má 21 úrovně přiblížení číslované 0 až 20. Na úrovni přiblížení 0 celého světa vyhovuje jedna dlaždice:

![Dlaždice World](./media/zoom-levels-and-tile-grid/world0.png)

Úroveň přiblížení 1 používá čtyři dlaždice k vykreslení na světě: odmocnina 2 x 2

![Vlevo nahoře dlaždice World](./media/zoom-levels-and-tile-grid/world1a.png)     ![Vpravo nahoře dlaždice World](./media/zoom-levels-and-tile-grid/world1c.png) 

![Vlevo dole dlaždici World](./media/zoom-levels-and-tile-grid/world1b.png)     ![Vpravo dole dlaždici World](./media/zoom-levels-and-tile-grid/world1d.png) 


Každou úroveň přiblížení následné čtyřmi vydělí dlaždice předchozímu, vytváření mřížku 2<sup>zvětšení</sup> x 2<sup>zvětšení</sup>. Úroveň zvětšení 20 je mřížka 2<sup>20</sup> x 2<sup>20</sup>, nebo 1 048 576 x 1 048 576 dlaždice (celkem 109,951,162,778 dlaždice).

Následující tabulka obsahuje úplný seznam hodnot pro přiblížení úrovně:

|úroveň přiblížení|měřidla nebo pixelů|měřidla straně vedle sebe|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0,6|152.8|
|19|0,3|76.4|
|20|0,15|38.2|

Dlaždice jsou volány přiblížení úroveň a x a y souřadnice odpovídající pozici na dlaždici na mřížky pro danou úroveň přiblížení.

Při určování kterou úroveň přiblížení pro použití, mějte na paměti, že každé umístění je na stabilní na jeho dlaždici. To znamená, že počet dlaždic, které jsou potřebné k zobrazení dané expanse území je závislá na konkrétní umístění přiblížení mřížky na celém světě. Například, pokud existují dva body 900 měřidla od sebe, je *může* trvat jenom tři dlaždice zobrazíte trasy mezi nimi na úroveň přiblížení 17. Ale pokud western bod je na pravé straně jeho dlaždici a bodem východní nalevo od jeho dlaždici, může trvat čtyři dlaždice:

![Zvětšení škálování demo](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Jakmile je určena úroveň přiblížení, x a y, lze vypočítat hodnoty. Je nejvyšší levé dlaždice v mřížce každý přiblížení x = 0, y = 0; na dlaždici vpravo dole je dokončeno z x = 2<sup>zvětšení -1</sup>, y = 2<sup>přiblížení-1</sup>.

Následuje přiblížení mřížky pro úroveň přiblížení 1:

![Přiblížení mřížky pro úroveň přiblížení 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
