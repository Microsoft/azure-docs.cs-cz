---
title: Azure škálovatelnost a výkonnostní cíle Storage - účty úložiště
description: Další informace o škálovatelnost a výkonnostní cíle, včetně kapacity a frekvence požadavků, příchozí a odchozí šířka pásma pro účty úložiště Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101475"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure škálovatelnost a výkonnostní cíle Storage pro účty úložiště

Tento článek obsahuje podrobnosti o škálovatelnost a výkonnostní cíle pro účty úložiště Azure. Škálovatelnost a výkonnostní cíle, které jsou tady uvedené špičkové cíle, ale jsou dosažitelný. Ve všech případech, frekvence požadavků a šířku pásma dosaženou úložiště účtu závisí na velikosti objekty uložené, vzory přístupu k optimalizováno a typu úlohy, která vaše aplikace provádí.

Nezapomeňte otestovat vaší služby k určení, zda jeho výkon a vyhovuje vašim požadavkům. Pokud je to možné vyhnout nečekaným špičkám míru přenosu dat a ujistěte se, že provoz je dobře distribuovaná napříč oddíly.

Když aplikace dosáhne limitu co dokáže zpracovat oddíl pro vaši úlohu, začne Azure Storage vrátí kód chyby 503 (zaneprázdněný Server) nebo kód chyby odpovědi 500 (časový limit operace). Pokud se vyskytnou chyby 503, zvažte úpravu aplikaci pomocí exponenciálního omezení rychlosti zásady opakování. Exponenciální regresí umožňuje zatížení v oddílu snížit a doběhu špičkami v provozu do daného oddílu.

## <a name="storage-account-scale-limits"></a>Limity škálování účtu úložiště

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limity škálování účtu úložiště výkon úrovně Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limity škálování poskytovatele prostředků úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob storage škálování cíle

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle

Další informace o škálování a výkonnostní cíle pro soubory Azure a Azure File Sync najdete v tématu [škálovatelnost a výkonnostní cíle Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Soubory typu Premium škálování cíle

Existují tři kategorie omezení ke zvážení pro soubory typu premium: účty úložiště, sdílené složky a soubory.

Příklad: Jednou sdílenou složkou může dosáhnout 100 000 vstupně-výstupních operací a jednoho souboru můžete škálovat až 5000 vstupně-výstupních operací. Ano Pokud budete mít tři soubory v jedné sdílené složky, maximálních hodnotách IOPs, můžete získat z této sdílené složky je například 15 000.

#### <a name="premium-file-share-limits"></a>Limity sdílené složky souboru úrovně Premium

> [!IMPORTANT]
> Omezení účtů úložiště platí pro všechny sdílené složky. Škálování až maximální počet účtů úložiště je pouze dosažitelný, pokud existuje pouze jedna sdílená složka na účet úložiště.

|Oblast  |Cíl  |
|---------|---------|
|Velikost min, zřízené                        |100 GiB      |
|Maximální počet zřízení velikost                        |100 TiB      |
|Minimální velikost zvětšit nebo zmenšit    |1 GiB      |
|Standardní hodnoty vstupně-výstupních operací    |1 vstupně-výstupních operací za GiB maximálně 100 000|
|Shlukování vstupně-výstupních operací    |3 x IOPS za GiB maximálně 100 000|
|Frekvence odchozího přenosu dat         |60 MiB/s + 0,06 * zřízené GiB        |
|Rychlost příchozího přenosu dat| 40 MiB/s + 0,04 * zřízené GiB |
|Maximální počet snímků        |200       |

#### <a name="premium-file-limits"></a>Omezení souboru Premium

|Oblast  |Cíl  |
|---------|---------|
|Velikost                  |1 TiB         |
|Maximální počet vstupně-výstupních operací na soubor     |5 000         |
|Souběžné obslužné rutiny    |2 000         |

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle

Azure File Sync byly navržené s cílem neomezené využití, ale neomezené využití není vždy možné. Následující tabulka označuje hranice testování od Microsoftu a také určuje cíle, které jsou pevných limitů:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue storage škálování cíle

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table storage škálování cíle

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Další informace najdete v tématech

- [Podrobnosti o cenách Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md)
- [Replikace Azure Storage](../storage-redundancy.md)
- [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../storage-performance-checklist.md)