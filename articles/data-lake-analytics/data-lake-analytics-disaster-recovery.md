---
title: Pokyny pro zotavení po havárii pro Azure Data Lake Analytics
description: Zjistěte, jak naplánovat zotavení po havárii pro vaše účty Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498886"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Pokyny pro zotavení po havárii pro Azure Data Lake Analytics

Azure Data Lake Analytics je služba analytických úloh na vyžádání, která zjednodušuje práci s velkými objemy dat. Namísto nasazení, konfigurace a ladění hardwaru můžete psát dotazy, transformovat data a extrahovat cenné informace. Analytická služba dokáže okamžitě zpracovávat úlohy libovolného rozsahu – stačí nastavit, jaký výkon potřebujete. Za úlohu platíte jenom tehdy, když je spuštěná, což je nákladově efektivní. Tento článek obsahuje pokyny o tom, jak chránit úlohy z výjimečných výjimečnými výpadky celé oblasti nebo nechtěným odstraněním.

## <a name="disaster-recovery-guidance"></a>Pokyny pro zotavení po havárii

Při použití Azure Data Lake Analytics, je důležité si můžete připravit své vlastní plán zotavení po havárii. Tento článek pomůže vás k sestavení plánu zotavení po havárii. Existují další prostředky, které vám pomůžou vytvářet vlastní plán:
- [Selhání a zotavení po havárii pro aplikace Azure](/azure/architecture/reliability/disaster-recovery)
- [Technické pokyny k odolnosti Azure](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Osvědčené postupy a pokyny případě

Opakovaná úloha U-SQL můžete spustit v rámci účtu ADLA v oblasti, která čte a zapisuje tabulky U-SQL, jakož i nestrukturovaná data.  Příprava na havárii pomocí následujících kroků:

1. Vytvoření účtů ADLA a ADLS v sekundární oblasti, který se použije během výpadků.

   > [!NOTE]
   > Vzhledem k tomu, že názvy účtů jsou globálně jedinečné, použijte konzistentní schéma pojmenování, určující účtu, který je sekundární.

2. Pro Nestrukturovaná data, odkazovat [pokyny pro zotavení po havárii pro data ve službě Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Pro strukturovaná data uložená v ADLA tabulek a databází vytvořte kopie metadat artefakty, jako jsou databáze, tabulky, funkce vracející tabulku a sestavení. Je potřeba pravidelně znovu synchronizujte tyto artefakty, když dojde k změnám v produkčním prostředí. Například nově vloženou data mají replikovat do sekundární oblasti tak, že kopírování dat a vkládání do sekundární tabulky.

   > [!NOTE]
   > Tyto názvy objektů jsou omezená na sekundární účet a nejsou globálně jedinečný, takže mají stejné názvy jako primární produkčního účtu.

Během výpadku je potřeba aktualizovat skripty tak, že vstupní cesta bodu na sekundární koncový bod. Uživatelé pak odešlete své úlohy do i účet ADLA v sekundární oblasti. Výstup úlohy se poté zapíšou do účtu ADLA a ADLS v sekundární oblasti.

## <a name="next-steps"></a>Další postup

[Pokyny pro zotavení po havárii pro data ve službě Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
