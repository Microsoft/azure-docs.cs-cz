---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Analytics
description: Přečtěte si, jak plánovat zotavení po havárii pro vaše účty Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889766"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Pokyny pro zotavení po havárii pro Azure Data Lake Analytics

Azure Data Lake Analytics je služba analytických úloh na vyžádání, která zjednodušuje práci s velkými objemy dat. Namísto nasazení, konfigurace a ladění hardwaru můžete psát dotazy, transformovat data a extrahovat cenné informace. Analytická služba dokáže okamžitě zpracovávat úlohy libovolného rozsahu – stačí nastavit, jaký výkon potřebujete. Za úlohu platíte jenom tehdy, když je spuštěná, což je nákladově efektivní. Tento článek obsahuje pokyny, jak chránit úlohy před výpadky v celé oblasti nebo náhodným odstraněním.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii

Při použití Azure Data Lake Analytics je důležité, abyste si připravili vlastní plán zotavení po havárii. Tento článek vám pomůže vytvořit plán zotavení po havárii. Existují další zdroje, které vám mohou pomoci vytvořit vlastní plán:
- [Selhání a zotavení po havárii pro aplikace Azure](/azure/architecture/reliability/disaster-recovery)
- [Technické pokyny k odolnosti Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Doporučené postupy a pokyny ke scénáři

Můžete spustit opakovanou úlohu U-SQL v účtu ADLA v oblasti, která čte a zapisuje tabulky U-SQL, stejně jako nestrukturovaná data.  Připravte se na katastrofu tím, že podniknete tyto kroky:

1. Vytvořte účty ADLA a ADLS v sekundární oblasti, která bude použita během výpadku.

   > [!NOTE]
   > Vzhledem k tomu, že názvy účtů jsou globálně jedinečné, použijte konzistentní schéma pojmenování, které označuje, který účet je sekundární.

2. Pro nestrukturovaná data se odkazy na [pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Pro strukturovaná data uložená v tabulkách a databázích ADLA vytvořte kopie artefaktů metadat, jako jsou databáze, tabulky, funkce s hodnotou tabulky a sestavení. Je třeba pravidelně resync tyto artefakty při provádění změn v produkčním prostředí. Například nově vložená data musí být replikována do sekundární oblasti zkopírováním dat a vložením do sekundární tabulky.

   > [!NOTE]
   > Tyto názvy objektů jsou vymezeny na sekundární účet a nejsou globálně jedinečné, takže mohou mít stejné názvy jako v účtu primární výroby.

Během výpadku je třeba aktualizovat skripty tak, aby vstupní cesty přejděte na sekundární koncový bod. Potom uživatelé odešlí své úlohy na účet ADLA v sekundární oblasti. Výstup úlohy pak bude zapsán do účtu ADLA a ADLS v sekundární oblasti.

## <a name="next-steps"></a>Další kroky

[Pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
