---
title: cíle škálování a výkonu služby Azure Storage
description: Další informace o škálovatelnost a výkonnostní cíle, včetně kapacity a frekvence požadavků, příchozí a odchozí šířka pásma pro účty úložiště Azure úrovně standard.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 93e09f3ab6780eb9ce7fa29b4554b53d796b6837
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564948"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Azure škálovatelnost a výkonnostní cíle Storage pro účty úložiště úrovně standard

Tento článek obsahuje podrobnosti o škálovatelnost a výkonnostní cíle pro účty úložiště Azure úrovně standard. Škálovatelnost a výkonnostní cíle, které jsou tady uvedené špičkové cíle, ale jsou dosažitelný. Ve všech případech, frekvence požadavků a šířku pásma dosaženou úložiště účtu závisí na velikosti objekty uložené, vzory přístupu k optimalizováno a typu úlohy, která vaše aplikace provádí. 

Nezapomeňte otestovat vaší služby k určení, zda jeho výkon a vyhovuje vašim požadavkům. Pokud je to možné vyhnout nečekaným špičkám míru přenosu dat a ujistěte se, že provoz je dobře distribuovaná napříč oddíly.

Když aplikace dosáhne limitu co dokáže zpracovat oddíl pro vaši úlohu, začne Azure Storage vrátí kód chyby 503 (zaneprázdněný Server) nebo kód chyby odpovědi 500 (časový limit operace). Pokud se vyskytnou chyby 503, zvažte úpravu aplikaci pomocí exponenciálního omezení rychlosti zásady opakování. Exponenciální regresí umožňuje zatížení v oddílu snížit a doběhu špičkami v provozu do daného oddílu.

## <a name="standard-storage-account-scale-limits"></a>Limity škálování účtu úložiště úrovně Standard
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limity škálování poskytovatele prostředků úložiště 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob storage škálování cíle
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle
Další informace o škálování a výkonnostní cíle pro soubory Azure a Azure File Sync najdete v tématu [škálovatelnost a výkonnostní cíle Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle
Azure File Sync byly navržené s cílem neomezené využití, ale neomezené využití není vždy možné. Následující tabulka označuje hranice testování od Microsoftu a také určuje cíle, které jsou pevných limitů:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure Queue storage škálování cíle
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure Table storage škálování cíle
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Viz také
* [Podrobnosti o cenách Storage](https://azure.microsoft.com/pricing/details/storage/)
* [Předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md)
* [Replikace Azure Storage](../storage-redundancy.md)
* [Kontrolní seznam pro výkon a škálovatelnost služby Microsoft Azure Storage](../storage-performance-checklist.md)

