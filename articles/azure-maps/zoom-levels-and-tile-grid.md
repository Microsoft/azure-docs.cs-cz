---
title: Úrovně přiblížení a Mřížka ve službě Azure Maps dlaždic | Dokumentace Microsoftu
description: Další informace o úrovních přiblížení a Mřížka ve službě Azure Maps dlaždic
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8eae5f258eaa899dc60e1e1cc066241bcff54970
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494074"
---
# <a name="zoom-levels-and-tile-grid"></a>Úrovně přiblížení a mřížka dlaždic
Azure Maps použít systém souřadnic projekci Mercator Kulovité (EPSG: 3857).

Čtvereček dlaždice se dělí na světě. Vykreslení (rastrové) má 21 úrovně přiblížení číslem 0 až 20. Vykreslení (Vector) má 23 úrovně přiblížení, číslované 0 až 22. Na úrovni zvětšení 0 celý svět vejde na jednu dlaždici:

![Dlaždice na celém světě](./media/zoom-levels-and-tile-grid/world0.png)

Úroveň přiblížení 1 používá čtyři dlaždice k vykreslení na světě: Čtvereček 2 x 2

![Světové dlaždice vlevo nahoře](./media/zoom-levels-and-tile-grid/world1a.png)     ![Pravém horním rohu dlaždice World](./media/zoom-levels-and-tile-grid/world1c.png) 

![Světové dlaždice vlevo dole](./media/zoom-levels-and-tile-grid/world1b.png)     ![Světové dlaždice dole vpravo](./media/zoom-levels-and-tile-grid/world1d.png) 


Každá úroveň přiblížení následné. quad celočíselně vydělí dlaždice předchozímu vytvoření tabulky 2<sup>přiblížení</sup> x 2<sup>přiblížení</sup>. Úroveň zvětšení 22 je mřížka 2<sup>22</sup> x 2<sup>22</sup>, nebo 4,194,304 x 4,194,304 dlaždice (celkem 17,592,186,044,416 dlaždice).

Následující tabulka obsahuje úplný seznam hodnot pro úrovně přiblížení:

|Úroveň přiblížení|Měření/pixelů|Měření/dlaždici na straně|
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
|18|0.6|152.8|
|19|0.3|76.4|
|20|0,15|38.2|
|21|0,075|19.1|
|22|0.0375|9.55|

Dlaždice jsou volány přiblížení úroveň x a y souřadnice odpovídá pozici na dlaždici na mřížce pro danou úroveň přiblížení.

Při určování, jaké úroveň přiblížení pro použití, mějte na paměti, že každé umístění je do pevné umístění na její dlaždici. To znamená, že je počet dlaždic, které jsou potřebné k zobrazení dané expanse území závisí na konkrétní umístění přiblížení mřížky na celém světě. Například pokud jsou k dispozici dva body 900 měřiče od sebe, je *může* trvat jenom tři dlaždice k zobrazení postupu mezi nimi úrovni zvětšení 17. Nicméně pokud západní bod na pravé straně její dlaždici a východní bod nalevo od jeho dlaždici, může trvat čtyři dlaždice:

![Ukázka měřítko přiblížení](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Jakmile se určí úroveň přiblížení, x a y hodnot je možné vypočítat. Horní levé dlaždice v každé mřížce přiblížení je x = 0, y = 0; na dlaždici vpravo dole je dokončeno z x = 2<sup>zoom -1</sup>, y = 2<sup>1 zvětšení</sup>.

Tady je mřížka přiblížení pro úroveň přiblížení 1:

![Přiblížení mřížky pro úroveň přiblížení 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
