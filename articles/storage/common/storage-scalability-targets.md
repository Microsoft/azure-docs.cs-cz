---
title: Škálovatelnost a cíle výkonnosti Azure Storage | Dokumentace Microsoftu
description: Další informace o škálovatelnost a výkonnostní cíle Azure Storage, včetně kapacity a frekvence požadavků, příchozí a odchozí šířka pásma pro oba účty úložiště úrovně standard a premium. Seznamte se s cílům výkonnosti pro oddíly v rámci všech služeb Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 248ec73b09c25c162c5bdc77ceb18e610d604b77
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052169"
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Škálovatelnost a výkonnostní cíle Azure Storage
## <a name="overview"></a>Přehled
Tento článek popisuje témata škálovatelnost a výkon pro službu Azure Storage. Souhrnné informace o dalších omezení v Azure, najdete v článku [předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md).

> [!NOTE]
> Všechny účty úložiště spustit na nové bez stromové struktury síťové topologie a podporu škálovatelnost a výkonnostní cíle, které jsou popsané v tomto článku, bez ohledu na to, když byly vytvořeny. Další informace o architektuře ploché sítě služby Azure Storage a na škálovatelnost, naleznete v tématu [Microsoft Azure Storage: A vysoce dostupné služby cloudového úložiště se silnou konzistencí](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> Škálovatelnost a výkonnostní cíle, které jsou tady uvedené špičkové cíle, ale jsou dosažitelný. Ve všech případech, frekvence požadavků a šířku pásma dosaženou úložiště účtu závisí na velikosti objekty uložené, vzory přístupu k optimalizováno a typu úlohy, která vaše aplikace provádí. Nezapomeňte otestovat vaší služby k určení, zda jeho výkon a vyhovuje vašim požadavkům. Pokud je to možné vyhnout nečekaným špičkám míru přenosu dat a ujistěte se, že provoz je dobře distribuovaná napříč oddíly.
> 
> Když aplikace dosáhne limitu co dokáže zpracovat oddíl pro vaši úlohu, začne Azure Storage vrátí kód chyby 503 (zaneprázdněný Server) nebo kód chyby odpovědi 500 (časový limit operace). Pokud k těmto chybám dochází, potom aplikace měla použít zásadu exponenciálního omezení rychlosti pro opakované pokusy. Exponenciální regresí umožňuje zatížení v oddílu snížit a doběhu špičkami v provozu do daného oddílu.
> 
> 

Potřeb svojí aplikace překročení cíle škálovatelnosti z jednoho účtu úložiště, můžete vytvářet aplikace pomocí více účtů úložiště. Pak můžete dělit datové objekty mezi těmito účty úložiště. Zobrazit [ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/) informace o cenách.

## <a name="scalability-targets-for-a-storage-account"></a>Cíle škálovatelnosti účtu úložiště
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob storage škálování cíle
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure soubory měřítko cíle
Další informace o škálování a výkonnostní cíle pro soubory Azure a Azure File Sync najdete v tématu [škálovatelnost a výkonnostní cíle Azure Files](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure File Sync měřítko cíle
Pomocí služby Azure File Sync jsme jste se pokusili co nejvíc návrhu pro neomezené využití, ale to není vždy možné. Následující tabulka označuje hranice naše testování a cíle, které jsou ve skutečnosti pevných limitů:

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
* [Microsoft Azure Storage: Vysoce dostupné služby cloudového úložiště se silnou konzistenci](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

