---
title: Přesunutí prostředků Azure do jiné oblasti
description: Poskytuje přehled přesunutí prostředků Azure napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485205"
---
# <a name="moving-azure-resources-across-regions"></a>Přesouvání prostředků Azure napříč oblastmi

Tento článek obsahuje informace o přesunutí prostředků Azure napříč oblastmi Azure.

Geografické oblasti Azure, oblasti a zóny dostupnosti tvoří základ globální infrastruktury Azure. [Geografické oblasti](https://azure.microsoft.com/global-infrastructure/geographies/) Azure obvykle obsahují dvě nebo více [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Oblast je oblast v rámci zeměpisné oblasti, obsahující zóny dostupnosti a více datových center. 

Po nasazení prostředků v konkrétní oblasti Azure, existuje řada důvodů, které můžete chtít přesunout prostředky do jiné oblasti.

- **Zarovnat na spuštění oblasti**: Přesuňte prostředky do nově zavedené oblasti Azure, která nebyla dříve dostupná.
- **Zarovnat pro služby/funkce:** Přesuňte prostředky, abyste využili výhod služeb nebo funkcí, které jsou k dispozici v určité oblasti.
- **Reakce na vývoj podnikání**: Přesun zdrojů do oblasti v reakci na obchodní změny, jako jsou fúze nebo akvizice.
- **Zarovnat blízkost**: Přesuňte prostředky do místní oblasti vaší firmy.
- **Splnění požadavků na data**: Přesuňte prostředky tak, aby byly v souladu s požadavky na rezidenci dat nebo s potřebami klasifikace dat. [Další informace](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf).
- **Reagujte na požadavky nasazení**: Přesuňte prostředky, které byly nasazeny omylem, nebo se přesuňte v reakci na potřeby kapacity. 
- **Reakce na vyřazení z provozu**: Přesun zdrojů z důvodu vyřazení regionů z provozu.

## <a name="move-process"></a>Přesunout proces

Skutečný proces přesunutí závisí na zdrojích, které přesouváte. Existují však některé běžné klíčové kroky:

- Ověřit požadavky : Mezi požadavky patří zajištění, zda jsou potřebné prostředky k dispozici v cílové oblasti, kontrola dostatečné **kvóty**a ověření, zda má vaše předplatné přístup k cílové oblasti.
- **Analyzovat závislosti**: Vaše prostředky mohou mít závislosti na jiných prostředcích. Před přesunutím zjistěte závislosti tak, aby přesunuté prostředky fungovaly podle očekávání po přesunutí.
- **Příprava na přesun**: Toto jsou kroky, které provedete v primární oblasti před přesunutím. Například budete muset exportovat šablonu Azure Resource Manager nebo začít replikovat prostředky ze zdroje do cíle.
- **Přesunutí prostředků**: Způsob přesunutí prostředků závisí na tom, co jsou. Možná budete muset nasadit šablonu v cílové oblasti nebo převzetí služeb při selhání prostředky k cíli.
- **Zahodit cílové prostředky**: Po přesunutí prostředků se můžete podívat na prostředky v cílové oblasti a rozhodnout se, zda je něco, co nepotřebujete.
- **Potvrzení přesunu**: Po ověření prostředků v cílové oblasti mohou některé prostředky vyžadovat konečnou akci potvrzení. Například v cílové oblasti, která je nyní primární oblast, možná budete muset nastavit zotavení po havárii do nové sekundární oblasti. 
- **Vyčistit zdroj**: Nakonec po všem, co je v provozu v nové oblasti, můžete vyčistit a vyřadit prostředky, které jste vytvořili pro přesun a prostředky v primární oblasti.



## <a name="next-steps"></a>Další kroky

Seznam, které prostředky podporují přesunutí napříč oblastmi, naleznete v tématu [Přesunutí podpory operací pro zdroje](region-move-support.md).
