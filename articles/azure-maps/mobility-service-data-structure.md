---
title: Datové struktury služby mobility v Azure Maps | Mapy Microsoft Azure
description: V tomto článku se seznámíte se společnými poli a datovými strukturami vrácenými prostřednictvím služby Microsoft Azure Maps mobility.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910726"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Datové struktury ve službě Azure Maps mobility

V tomto článku se seznámíte s pojmem oblasti metro ve [službě Azure Maps mobility](https://aka.ms/AzureMapsMobilityService) a některými běžnými poli vrácenými prostřednictvím služeb při jejich dotazování na zastávky a řádky veřejného přenosu. Než začnete s rozhraními API služby mobility, doporučujeme projít tento článek. Tato společná pole probereme níže.

## <a name="metro-area"></a>Oblast Metro

Data služby mobility jsou rozdělená na podporované oblasti metro. Oblasti metro nenásledují po hranicích měst, oblast Metro může obsahovat více měst, například hustě naplněné město a jeho okolní města. a země/oblast může být jedna oblast Metro. 

`metroID` je ID oblasti metro, které se dá použít k volání [rozhraní API pro získání informací o oblasti Get Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo) k vyžádání podporovaných typů přenosů a dalších podrobností pro oblast Metro, jako jsou například tranzitní úřady a aktivní výstrahy. K vyžádání podporovaných oblastí metro a metroIDs můžete použít Azure Maps získat rozhraní API Metro. ID oblasti metro se mohou změnit.

**metroID:** 522 **Název:** Seattle-Tacoma-Bellevue

![Praha – Metro – oblast](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID zastavení

Přechody na zastávky můžou být odkazovány dvěma typy ID, identifikátorem [GFTS (General tranzitní kanál Specification)](https://gtfs.org/) (označovaným jako stopKey) a id zastavení Azure Maps (označovaným jako stopid). Při odkazování na zastavení v průběhu času je navrženo použití ID stop Azure Maps, protože toto ID je stabilnější a nebude se nejspíš měnit, dokud existuje fyzické zastavení. ID stop GTFS se často aktualizuje, například v případě, že ho poskytovatel GTFS potřebuje změnit nebo když se uvolní nová verze GTFS, i když se u fyzického zastavení nezměnila.

Abyste mohli začít, můžete požádat o přechod do blízkosti [provozu pomocí rozhraní získat rozhraní API pro nejbližší](https://aka.ms/AzureMapsMobilityNearbyTransit)přenos.

## <a name="line-groups-and-lines"></a>Řádky a skupiny řádků

Služba mobility používá paralelní datový model pro spojnice a spojnicové skupiny, aby bylo lépe zabývat se změnami děděnými z [GTFSch](https://gtfs.org/) tras a datových modelů cest.


### <a name="line-groups"></a>Skupiny řádků

Řádková skupina je entita, která seskupuje všechny řádky, které jsou logicky součástí stejné skupiny. Spojnicová skupina obvykle bude obsahovat dva řádky, jeden z bodu A na B a druhý návrat z bodu B do A, který patří ke stejné veřejné přenosové agentuře a má stejné číslo řádku. Mohou však nastat případy, kdy skupina řádků obsahuje více než dva řádky nebo pouze jeden řádek v rámci něj.


### <a name="lines"></a>Spojnice

Jak je popsáno výše, každá skupina řádků se skládá ze sady řádků. Často jednotlivé řádky popisují směr a jednotlivé spojnicové skupiny se skládají ze dvou řádků. Existují však případy, kdy se skládá z více řádků, například je řádek, který někdy provádí detreke v určitém okolí a někdy není a je provozován v obou případech za stejným číslem řádku a existují i jiné případy, kdy řádek g kupiny se skládá z jednoho řádku, například kruhové spojnice s jedním směrem.

Chcete-li začít, můžete požádat o skupiny řádků pomocí [rozhraní API pro přenos řádků](https://aka.ms/AzureMapsMobilityTransitLine) a později přejít k podrobnostem na řádky.


## <a name="next-steps"></a>Další kroky

Informace o tom, jak požadovat přenosová data pomocí služby mobility:

> [!div class="nextstepaction"]
> [Požadavky na přenosová data](how-to-request-transit-data.md)

Informace o tom, jak vyžádat data v reálném čase pomocí služby mobility:

> [!div class="nextstepaction"]
> [Jak vyžádat data v reálném čase](how-to-request-real-time-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)
