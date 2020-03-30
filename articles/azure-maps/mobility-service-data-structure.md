---
title: Datové struktury služby Mobility Service v Mapách Azure| Mapy Microsoft Azure
description: V tomto článku se dozvíte o běžných polích a datových strukturách vrácených prostřednictvím služeb mobility Map Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334444"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Datové struktury ve službě Mobility Azure Maps

Tento článek představuje koncept oblasti metra ve [službě Mobility Azure Maps](https://aka.ms/AzureMapsMobilityService). Diskutujeme o některých běžných polích, která jsou vrácena při dotazování této služby na zastávky a řádky veřejné dopravy. Doporučujeme přečíst si tento článek před vývojem s nastaveními API služby mobility.

## <a name="metro-area"></a>Oblast metra

Data služby Mobility Service jsou seskupena podle podporovaných oblastí metra. Oblasti metra nesledují hranice města. Oblast metra může obsahovat více měst, hustě obydlené město a okolní města. Ve skutečnosti může být země nebo oblast jednou oblastí metra. 

Jedná `metroID` se o ID oblasti metra, které lze použít k volání [Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Pomocí rozhraní API "Get Metro" služby Azure Maps můžete požádat o typy tranzitu, tranzitní agentury, aktivní výstrahy a další podrobnosti o zvoleném metru. Můžete také požádat o podporované oblasti metra a metroIDs. ID oblasti metra se mohou změnit.

**metroID:** 522 **Jméno:** Seattle-Tacoma-Bellevue

![Seattle-metro-oblast](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zastavit ID

Tranzitní zastávky mohou odkazovat podle dvou typů ID, [ID specifikace general transit feed (GFTS)](https://gtfs.org/) a ID zastavení Map Azure. ID GFTS se označuje jako stopKey a ID zastavení map Azure se označuje jako stopID. Když často odkazujete na tranzitní zastávky, doporučujeme použít ID zastavení Azure Maps. stopID je stabilnější a pravděpodobně zůstane stejná, dokud existuje fyzická zastávka. ID zastavení GTFS se aktualizuje častěji. Například GTFS stop ID lze aktualizovat na požadavek poskytovatele GTFS nebo při vydání nové verze GTFS. Přestože fyzická zastávka neměla žádnou změnu, ID zastavení GTFS se může změnit.

Chcete-li začít, můžete požádat o zastavení dopravy v okolí pomocí [rozhraní Get Near Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Skupiny řádků a čáry

Služba mobility používá paralelní datový model pro čáry a skupiny linek. Tento model se používá k lepšímu řešení změn zděděných ze tras [GTFS](https://gtfs.org/) a dat o cestách.


### <a name="line-groups"></a>Skupiny řádků

Řádková skupina je entita, která seskupuje všechny řádky, které jsou logicky součástí stejné skupiny. Skupina řádků obvykle obsahuje dva řádky, jeden z bodu A do Bodu B a druhý se vrací z bodu B do bodu A. Obě linky by patřily stejné agentuře veřejné dopravy a měly by stejné číslo linky. Mohou však existovat případy, kdy řádková skupina obsahuje více než dva řádky nebo pouze jeden řádek.


### <a name="lines"></a>Spojnice

Jak je popsáno výše, každá skupina řádků se skládá ze sady řádků. Každá skupina řádků se skládá ze dvou řádků a každá čára popisuje směr.  Existují však případy, ve kterých více řádků tvoří skupinu řádků. Například, tam je linka, která někdy objížďky přes určité čtvrti a někdy ne. V obou případech pracuje pod stejným číslem linky. Také skupina řádků může být složena z jednoho řádku. Kruhová čára s jedním směrem je ling skupina s jednou čárou.

Chcete-li začít, můžete požádat o skupiny řádků pomocí [rozhraní Get Transit Line API](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak požádat o údaje o tranzitu pomocí služby Mobility Service:

> [!div class="nextstepaction"]
> [Jak požádat o údaje o tranzitu](how-to-request-transit-data.md)

Naučte se, jak požádat o data v reálném čase pomocí služby Mobility Service:

> [!div class="nextstepaction"]
> [Jak požádat o data v reálném čase](how-to-request-real-time-data.md)

Projděte si dokumentaci k rozhraní API služby Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
