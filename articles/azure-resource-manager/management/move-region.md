---
title: Přesunutí prostředků Azure do jiné oblasti
description: Poskytuje přehled o přesouvání prostředků Azure napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75485205"
---
# <a name="moving-azure-resources-across-regions"></a>Přesun prostředků Azure napříč oblastmi

Tento článek poskytuje informace o přesunu prostředků Azure napříč oblastmi Azure.

Geografické grafy Azure, oblasti a Zóny dostupnosti tvoří základ globální infrastruktury Azure. Geografické [grafy](https://azure.microsoft.com/global-infrastructure/geographies/) Azure obvykle obsahují dvě nebo víc [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Oblast je oblast v rámci geografické oblasti, která obsahuje Zóny dostupnosti a více datových center. 

Po nasazení prostředků v konkrétní oblasti Azure existuje několik důvodů, proč je vhodné přesunout prostředky do jiné oblasti.

- **Zarovnat ke konkrétnímu spuštění oblasti**: přesun svých prostředků do nově zavedené oblasti Azure, která nebyla dřív dostupná.
- **Zarovnání pro služby/funkce**: přesunout prostředky, abyste mohli využívat služby nebo funkce, které jsou k dispozici v konkrétní oblasti.
- **Reakce na obchodní vývoj**: Přesunutí prostředků do oblasti v reakci na změny v podniku, jako je fúze nebo akvizice.
- **Zarovnání k blízkosti**: Přesunutí prostředků do místní oblasti do vaší firmy.
- **Splnění požadavků na data**: Přesunutí prostředků, aby se daly v souladu s požadavky na data a nároky na data a klasifikace dat. [Další informace](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reakce na požadavky na nasazení**: přesunout prostředky, které byly nasazeny chybně, nebo je přesunout v reakci na kapacitu kapacity. 
- **Reakce na vyřazení z provozu**: Přesunutí prostředků z důvodu vyřazení oblastí z provozu.

## <a name="move-process"></a>Přesunout proces

Skutečný proces přesunutí závisí na prostředcích, které přesouváte. Existují však některé běžné klíčové kroky:

- **Ověřit požadavky**: požadavky zahrnují kontrolu dostupnosti potřebných prostředků v cílové oblasti, kontrolu, zda máte dostatečnou kvótu a zda má vaše předplatné přístup k cílové oblasti.
- **Analyzovat závislosti**: vaše prostředky můžou mít závislosti na jiných prostředcích. Před přesunutím Zjistěte závislosti, aby přesunuté prostředky nadále fungovaly podle očekávání po přesunutí.
- **Příprava na přesun**: Jedná se o kroky, které v primární oblasti převezmete před přesunutím. Například může být nutné exportovat šablonu Azure Resource Manager nebo zahájit replikaci prostředků ze zdroje do cíle.
- **Přesunutí prostředků**: způsob přesunutí prostředků závisí na tom, co jsou. Je možné, že budete muset nasadit šablonu v cílové oblasti nebo převzít prostředky do cíle.
- **Zahození cílových prostředků**: po přesunutí prostředků se můžete podívat na prostředky v cílové oblasti a rozhodnout se, jestli nepotřebujete nic.
- **Potvrzení přesunutí**: po ověření prostředků v cílové oblasti můžou některé prostředky vyžadovat konečnou akci potvrzení. Například v cílové oblasti, která je teď primární oblastí, možná budete muset nastavit zotavení po havárii do nové sekundární oblasti. 
- **Vyčištění zdroje**: nakonec po zprovoznění všeho a spuštění v nové oblasti můžete vyčistit a vyřadit z provozu prostředky, které jste vytvořili pro přesun, a prostředky v primární oblasti.



## <a name="next-steps"></a>Další kroky

Seznam prostředků, které podporují přesun mezi oblastmi, najdete v tématu [Podpora operací přesunutí pro prostředky](region-move-support.md).
