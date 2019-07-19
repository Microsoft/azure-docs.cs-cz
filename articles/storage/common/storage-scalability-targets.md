---
title: Azure Storage škálovatelnost a výkonnostní cíle – účty úložiště
description: Seznamte se s cíli škálovatelnosti a výkonu, včetně kapacity, míry požadavků a příchozí a odchozí šířky pásma pro účty služby Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326952"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure Storage škálovatelnost a výkonnostní cíle pro účty úložiště

Tento článek podrobně popisuje škálovatelnost a výkonnostní cíle pro účty Azure Storage. Zde uvedené cíle škálovatelnosti a výkonu jsou špičkové cíle, ale lze je dosažitelný. Frekvence požadavků a šířka pásma dosažené vaším účtem úložiště závisí ve všech případech na velikosti uložených objektů, využívaných vzorech přístupu a typu zatížení, které vaše aplikace provádí.

Ujistěte se, že jste službu otestovali, abyste zjistili, jestli její výkon vyhovuje vašim požadavkům. Pokud je to možné, vyhněte se náhlým špičkám v oblasti provozu a zajistěte, aby byl provoz v různých oddílech dobře rozložený.

Když vaše aplikace dosáhne limitu toho, co může oddíl zpracovat pro vaše zatížení, Azure Storage začne vracet kód chyby 503 (zaneprázdněný serverem) nebo s kódem chyby 500 (časový limit operace). Pokud dojde k chybám 503, zvažte úpravu aplikace tak, aby pro opakované pokusy používala exponenciální omezení rychlosti zásady. Exponenciální omezení rychlosti umožňuje snížit zatížení oddílu a rozsvítit špičky v provozu do tohoto oddílu.

## <a name="storage-account-scale-limits"></a>Omezení škálování účtu úložiště

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Omezení škálování účtu úložiště úrovně Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Omezení škálování poskytovatele prostředků úložiště

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Cíle škálování služby Azure Blob Storage

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle

Další informace o škálování a výkonu pro Azure Files a Azure File Sync najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> Limity účtu úložiště se vztahují na všechny sdílené složky. Horizontální navýšení kapacity účtů úložiště je možné jen tehdy, je-li k dispozici pouze jedna sdílená složka na jeden účet úložiště.
>
> Standardní sdílené soubory větší než 5 TiB jsou ve verzi Preview a mají určitá omezení.
> Seznam omezení a připojení k verzi Preview těchto větších souborů ke sdílení najdete v části [standardní sdílené složky](../files/storage-files-planning.md#standard-file-shares) v příručce pro plánování souborů Azure.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Cíle škálování souborů úrovně Premium

Existují tři kategorie omezení, které je třeba vzít v úvahu pro prémiové soubory: účty úložiště, sdílené složky a soubory.

Příklad: Jedna sdílená složka může dosahovat 100 000 vstupně-výstupních operací a jeden soubor se může škálovat až na 5 000 IOPS. Takže pokud máte například tři soubory v jedné sdílené složce, maximální IOPs, kterou můžete z této sdílené složky získat, je 15 000.

#### <a name="premium-file-share-limits"></a>Omezení sdílení souborů úrovně Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle

Azure File Sync byla navržena s cílem neomezeného využití, ale neomezené využití není vždy možné. Následující tabulka uvádí hranice testování Microsoftu a také uvádí, které cíle jsou pevné meze:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Cíle škálování služby Azure Queue Storage

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Cíle škálování služby Azure Table Storage

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Viz také:

- [Podrobnosti o cenách úložiště](https://azure.microsoft.com/pricing/details/storage/)
- [Limity, kvóty a omezení předplatného a služeb Azure](../../azure-subscription-service-limits.md)
- [Replikace Azure Storage](../storage-redundancy.md)
- [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../storage-performance-checklist.md)
