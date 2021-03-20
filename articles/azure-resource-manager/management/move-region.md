---
title: Přesunutí prostředků Azure do jiné oblasti
description: Poskytuje přehled o přesouvání prostředků Azure napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90007634"
---
# <a name="moving-azure-resources-across-regions"></a>Přesun prostředků Azure napříč oblastmi

Tento článek poskytuje informace o přesunu prostředků Azure napříč oblastmi Azure.

Geografické oblasti Azure, oblasti a zóny dostupnosti tvoří základ globální infrastruktury Azure. Geografické [grafy](https://azure.microsoft.com/global-infrastructure/geographies/) Azure obvykle obsahují dvě nebo víc [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Oblast je oblast v rámci geografické oblasti, která obsahuje Zóny dostupnosti a více datových center. 

Po nasazení prostředků v konkrétní oblasti Azure existuje několik důvodů, proč je vhodné přesunout prostředky do jiné oblasti.

- **Zarovnat ke konkrétnímu spuštění oblasti**: přesun svých prostředků do nově zavedené oblasti Azure, která nebyla dřív dostupná.
- **Zarovnání pro služby/funkce**: přesunout prostředky, abyste mohli využívat služby nebo funkce, které jsou k dispozici v konkrétní oblasti.
- **Reakce na obchodní vývoj**: Přesunutí prostředků do oblasti v reakci na změny v podniku, jako je fúze nebo akvizice.
- **Zarovnání k blízkosti**: Přesunutí prostředků do místní oblasti do vaší firmy.
- **Splnění požadavků na data**: Přesunutí prostředků, aby se daly v souladu s požadavky na data a nároky na data a klasifikace dat. [Další informace](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reakce na požadavky na nasazení**: přesunout prostředky, které byly nasazeny chybně, nebo je přesunout v reakci na kapacitu kapacity. 
- **Reakce na vyřazení z provozu**: Přesunutí prostředků z důvodu vyřazení oblastí z provozu.

## <a name="move-resources-with-resource-mover"></a>Přesunutí prostředků pomocí Resource stěhovací

Prostředky můžete přesunout do jiné oblasti pomocí programu [Azure Resource stěhovací](../../resource-mover/overview.md). Resource stěhovací poskytuje:

- Jediné centrum pro přesouvání prostředků napříč oblastmi.
- Zkrácená doba přesunutí a složitost. Všechno, co potřebujete, je na jednom místě.
- Jednoduché a konzistentní prostředí pro přesun různých typů prostředků Azure.
- Snadný způsob, jak identifikovat závislosti mezi prostředky, které chcete přesunout. To vám pomůže přesunout související prostředky dohromady, takže vše funguje podle očekávání v cílové oblasti, a to po přesunutí.
- Automatické vyčištění prostředků ve zdrojové oblasti, pokud je chcete po přesunutí odstranit.
- Testování. Můžete si vyzkoušet přesunutí a pak ho zahodit, pokud nechcete provést úplné přesunutí.

Prostředky můžete přesunout do jiné oblasti pomocí několika různých metod:

- **Zahájení přesouvání prostředků ze skupiny prostředků**: s touto metodou jste v rámci skupiny prostředků aktivovali přesunutí oblasti. Po výběru prostředků, které chcete přesunout, proces pokračuje v centru zdrojů pro kontrolu závislostí prostředků a orchestruje proces přesunutí. [Další informace](../../resource-mover/move-region-within-resource-group.md).
- **Začněte přesouvat prostředky přímo z centra pro** podávání prostředků: s touto metodou jste v oblasti přesunuli proces přesunutí přímo do centra. [Další informace](../../resource-mover/tutorial-move-region-virtual-machines.md).


## <a name="support-for-region-move"></a>Podpora pro přesun oblasti

V současné době můžete k přesunutí těchto prostředků do jiné oblasti použít Resource stěhovací:

- Virtuální počítače Azure a přidružené disky
- Síťové karty
- Skupiny dostupnosti
- Virtuální sítě Azure
- Veřejné IP adresy
- Skupiny zabezpečení sítě (NSG)
- Interní a veřejné nástroje pro vyrovnávání zatížení
- Databáze SQL Azure a elastické fondy

## <a name="region-move-process"></a>Proces přesunutí oblasti

Skutečný proces přesunutí prostředků mezi oblastmi závisí na prostředcích, které přesouváte. Existují však některé běžné klíčové kroky:

1. **Ověřit požadavky**: požadavky zahrnují kontrolu dostupnosti potřebných prostředků v cílové oblasti, kontrolu, zda máte dostatečnou kvótu a zda má vaše předplatné přístup k cílové oblasti.
2. **Analyzovat závislosti**: vaše prostředky můžou mít závislosti na jiných prostředcích. Před přesunutím Zjistěte závislosti, aby přesunuté prostředky nadále fungovaly podle očekávání po přesunutí.
3. **Příprava na přesun**: Jedná se o kroky, které v primární oblasti převezmete před přesunutím. Například může být nutné exportovat šablonu Azure Resource Manager nebo zahájit replikaci prostředků ze zdroje do cíle.
4. **Přesunutí prostředků**: způsob přesunutí prostředků závisí na tom, co jsou. Je možné, že budete muset nasadit šablonu v cílové oblasti nebo převzít prostředky do cíle.
5. **Zahození cílových prostředků**: po přesunutí prostředků se můžete podívat na prostředky v cílové oblasti a rozhodnout se, jestli nepotřebujete nic.
6. **Potvrzení přesunutí**: po ověření prostředků v cílové oblasti můžou některé prostředky vyžadovat konečnou akci potvrzení. Například v cílové oblasti, která je teď primární oblastí, možná budete muset nastavit zotavení po havárii do nové sekundární oblasti. 
7. **Vyčištění zdroje**: nakonec po zprovoznění všeho a spuštění v nové oblasti můžete vyčistit a vyřadit z provozu prostředky, které jste vytvořili pro přesun, a prostředky v primární oblasti.



## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](../../resource-mover/about-move-process.md) o procesu přesunu v nástroji Resource stěhovací.
