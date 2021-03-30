---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Analytics
description: Naučte se naplánovat zotavení po havárii pro účty Azure Data Lake Analytics.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: ce5e96bf464eebb9b91e3ffac9312e89df9cbabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220971"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Pokyny pro zotavení po havárii pro Azure Data Lake Analytics

Azure Data Lake Analytics je služba analytických úloh na vyžádání, která zjednodušuje práci s velkými objemy dat. Namísto nasazení, konfigurace a ladění hardwaru můžete psát dotazy, transformovat data a extrahovat cenné informace. Analytická služba dokáže okamžitě zpracovávat úlohy libovolného rozsahu – stačí nastavit, jaký výkon potřebujete. Za úlohu platíte jenom tehdy, když je spuštěná, což je nákladově efektivní. Tento článek poskytuje informace o tom, jak chránit vaše úlohy před vzácnými výpadky nebo neúmyslnými odstraněními v rámci oblastí.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii

Při použití Azure Data Lake Analytics je důležité připravit svůj vlastní plán zotavení po havárii. Tento článek vám pomůže s vytvořením plánu zotavení po havárii. K dispozici jsou další zdroje informací, které vám pomohou vytvořit vlastní plán:
- [Selhání a zotavení po havárii pro aplikace Azure](/azure/architecture/reliability/disaster-recovery)
- [Technické pokyny k odolnosti Azure](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Doporučené postupy a doprovodné materiály k scénářům

Můžete spustit opakující se úlohu U-SQL v účtu ADLA v oblasti, která čte a zapisuje tabulky U-SQL a také nestrukturovaná data.  Příprava na havárii pomocí následujících kroků:

1. Vytvořte účty ADLA a ADLS v sekundární oblasti, která se bude používat při výpadku.

   > [!NOTE]
   > Vzhledem k tomu, že názvy účtů jsou globálně jedinečné, použijte jednotné schéma pojmenování, které určuje, který účet je sekundární.

2. Pro nestrukturovaná data referenční [pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Pro strukturovaná data uložená v tabulkách a databázích ADLA vytvořte kopie artefaktů metadat, jako jsou databáze, tabulky, funkce vracející tabulku a sestavení. Pokud dojde ke změnám v produkčním prostředí, je potřeba pravidelně znovu synchronizovat tyto artefakty. Například nově vložená data musí být replikována do sekundární oblasti zkopírováním dat a vložením do sekundární tabulky.

   > [!NOTE]
   > Tyto názvy objektů jsou vymezeny na sekundární účet a nejsou globálně jedinečné, takže můžou mít stejné názvy jako v primárním produkčním účtu.

Během výpadku je potřeba aktualizovat skripty tak, aby vstupní cesty odkazovaly na sekundární koncový bod. Uživatelé pak odešlou své úlohy do účtu ADLA v sekundární oblasti. Výstup úlohy se pak zapíše do účtu ADLA a ADLS v sekundární oblasti.

## <a name="next-steps"></a>Další kroky

[Pokyny pro zotavení po havárii pro data v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
