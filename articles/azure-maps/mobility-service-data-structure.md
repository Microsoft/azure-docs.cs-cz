---
title: Mobility Service datové struktury v rámci služby Azure Maps | Dokumentace Microsoftu
description: Datové struktury ve službě Azure Maps Mobility Service
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735553"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Datové struktury ve službě Azure Maps Mobility Service

Tento článek představuje pojem Metro oblast v [služby Mobility Azure Maps](https://aka.ms/AzureMapsMobilityService) a některých běžných polí prostřednictvím služeb, vrátí, když je dotazován veřejné přenosu zastaví a řádky. Doporučujeme projít tento článek před zahájením navýšení kapacity s rozhraními API služby Mobility Service. Můžeme probírat tyto společných polí níže.

## <a name="metro-area"></a>Metro oblasti

Data služby mobility je rozdělit na podporované metro oblasti. Metro oblasti nepostupujte podle města hranice, metro oblasti může obsahovat více měst, například početnými Město a jeho okolního cities; a zemi/oblast může být jeden metro oblasti. 

`metroID` Je ID metro oblasti, který lze použít k volání [získat informace o API oblasti Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo) požadavku nepodporuje průchod typy a další podrobnosti o metro oblasti, jako je například přenosu úřady a aktivní výstrahy. Vyžadovat podporované metro oblasti a metroIDs můžete použít Azure Maps získat Metro API. ID Metro oblasti se mohou změnit.

**metroID:** 522 **název:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zastavit ID

Průchod zarážky lze odkazovat dvěma typy identifikátorů, [obecné přenosu kanálu specifikace (GFTS)](https://gtfs.org/) ID (označované jako stopKey) a Azure Maps zastavit ID (označované jako stopId). Při odkazování na přestane v čase, doporučujeme, abyste pomocí ID stop Azure Maps, protože toto ID je stabilnější a pravděpodobně nezmění, dokud existuje fyzické stop. GTFS stop ID se aktualizuje častěji, například v případě poskytovatele GTFS je potřeba ho změnit nebo je vydána nová verze GTFS, i když fyzické stop měl beze změny.

Pokud chcete začít, můžete požádat o zastaví blízké přenosu pomocí [získání rozhraní API blízké přenosu](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Skupiny řádků a čáry

Služba mobility používá paralelní datový model pro řádky a skupiny řádků, líp řešit změny zděděno z [GTFS](https://gtfs.org/) trasy a zkracuje dobu odezvy datového modelu.


### <a name="line-groups"></a>Skupiny řádků

Skupina řádku je entita, která seskupuje všechny řádky, které logicky jsou součástí stejné skupiny. Obvykle skupinu řádek bude obsahovat dva řádky, jeden přechod z bodu A na B, jiné vrací z bodu B do A, obě, který patří do stejné agentura dopravu a mají stejné číslo řádku. Může však být případy, ve kterých má skupina řádku více než dva řádky nebo v něm jen jeden řádek.


### <a name="lines"></a>řádky

Jak je popsáno výše, každá skupina se skládá ze sady řádků. Často každý řádek popisuje směru a každý řádek skupina se skládá ze dvou řádcích. Ale existují případy, ve které více řádků tvoří řádku skupinu, například existuje řádek, který někdy obchází prostřednictvím určitých okolí a někdy tak není a je provozována v obou případech v rámci stejné číslo řádku a existují další případy, ve kterém řádku g Skupina se skládá z jednoho řádku, například cyklické čáry s jeden směr.

Pokud chcete začít, můžete vyžádat pomocí skupiny řádků [získat řádek API přenosu](https://aka.ms/AzureMapsMobilityTransitLine) a novější hierarchií řádky.


## <a name="next-steps"></a>Další postup

Zjistěte, jak požádat o přenosu dat pomocí služby Mobility:

> [!div class="nextstepaction"]
> [Jak si vyžádat přenosu dat](how-to-request-transit-data.md)

Zjistěte, jak požádat o data v reálném čase pomocí služby Mobility:

> [!div class="nextstepaction"]
> [Jak si vyžádat data v reálném čase](how-to-request-real-time-data.md)

Projděte si dokumentaci rozhraní API služby Mobility Azure Maps

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
