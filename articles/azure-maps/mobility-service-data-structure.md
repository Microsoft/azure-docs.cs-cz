---
title: Datové struktury služby mobility (Preview) ve službě Microsoft Azure Maps
description: Seznamte se s tím, jak se data uspořádávají do oblastí Metro ve službě Azure Maps mobility Services (Preview). Podívejte se, která pole obsahují informace o zastavení a řádcích veřejného přenosu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904716"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Datové struktury ve službě Azure Maps mobility Services (Preview) 

> [!IMPORTANT]
> Služby Azure Maps mobility jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Tento článek představuje koncept oblasti Metro v [Azure Maps služby mobility](/rest/api/maps/mobility). Probereme některá společná pole, která se vrátí, když se tato služba dotáže na zastavení a řádky veřejného přenosu. Před vývojem s využitím rozhraní API služeb mobility doporučujeme tento článek přečíst.

## <a name="metro-area"></a>Oblast Metro

Data služby mobility (Preview) se seskupují podle podporovaných oblastí Metro. Oblasti metro nenásledují na hranicích měst. Oblast Metro může obsahovat více měst, hustě vyplněné města a okolní města. Ve skutečnosti může být země nebo oblast jedna oblast Metro. 

`metroID`Je ID oblasti metro, které se dá použít k volání [rozhraní API pro informace o oblasti Get Metro](/rest/api/maps/mobility/getmetroareainfopreview). Pomocí Azure Maps získat rozhraní API služby Metro pro vyžádání typů přenosů, přenosných úřadů, aktivních výstrah a dalších podrobností pro zvolenou službu Metro. Můžete si také vyžádat podporované oblasti metro a metroIDs. ID oblasti metro se mohou změnit.

**metroID:** 522   **Název:** Seattle-Tacoma-Bellevue

![Praha – Metro – oblast](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID zastavení

Přechody na zastávky můžou být odkazovány dvěma typy ID, identifikátorem [GFTS (General tranzitní kanál Specification)](http://gtfs.org/) a id zastavení Azure Maps. ID GFTS se označuje jako stopKey a ID stop Azure Maps je odkazováno jako stopID. Při častém odkazování na zastavení přenosu doporučujeme použít Azure Maps stop ID. stopID je více stabilní a může zůstat stejná, dokud existuje fyzické zastavení. ID stop GTFS se často aktualizuje. Například GTFS stop ID lze aktualizovat podle požadavku poskytovatele GTFS nebo při vydání nové verze GTFS. I když se fyzické zastavení žádné změny nezměnilo, může se změnit ID stop GTFS.

Začněte tím, že požádáte o okolní přenos, který se zastaví pomocí [rozhraní získat rozhraní API pro nejbližší přenos](/rest/api/maps/mobility/getnearbytransitpreview).

## <a name="line-groups-and-lines"></a>Řádky a skupiny řádků

Služba mobility (Preview) používá paralelní datový model pro řádky a skupiny řádků. Tento model se používá k lepšímu obchodování se změnami děděnými z [GTFSch](http://gtfs.org/) tras a dat cest.


### <a name="line-groups"></a>Skupiny řádků

Řádková skupina je entita, která seskupuje všechny řádky, které jsou logicky součástí stejné skupiny. Spojnicová skupina obvykle obsahuje dva řádky, jeden z bodu A na B a druhá se vrací z bodu B do. Oba řádky by patřily do stejné veřejné přenosového úřadu a mají stejné číslo řádku. Mohou však nastat případy, kdy skupina řádků obsahuje více než dva řádky nebo pouze jeden řádek v rámci něj.


### <a name="lines"></a>Spojnice

Jak je popsáno výše, každá skupina řádků se skládá ze sady řádků. Každá řádková skupina se skládá ze dvou řádků a každý řádek popisuje směr.  Existují však případy, kdy více řádků tvoří skupinu řádků. Například je k dispozici řádek, který někdy rozchází z určitého okolí a někdy ne. V obou případech funguje pod stejným číslem řádku. Spojnicová skupina může být také tvořena jedním řádkem. KRUHOVÁ čára s jedním směrem je Ling skupina s jedním řádkem.

Chcete-li začít, můžete požádat o skupiny řádků pomocí [rozhraní API pro přenosové linky](/rest/api/maps/mobility/gettransitlineinfopreview).


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak vyžádat přenosová data pomocí služeb mobility (Preview):

> [!div class="nextstepaction"]
> [Požadavky na přenosová data](how-to-request-transit-data.md)

Informace o tom, jak vyžádat data v reálném čase pomocí služeb mobility (Preview):

> [!div class="nextstepaction"]
> [Jak vyžádat data v reálném čase](how-to-request-real-time-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility (Preview)

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility Services](/rest/api/maps/mobility)