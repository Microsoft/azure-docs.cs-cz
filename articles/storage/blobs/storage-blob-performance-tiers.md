---
title: Úroveň výkonu úložiště objektů blob bloku Azure – Azure Storage
description: Úrovně výkonu pro Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063255"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Úroveň výkonu úložiště objektů blob bloku Azure

Vzhledem k tomu, že podniky nasazují výkonně citlivé aplikace v cloudu, je důležité mít možnosti pro nákladově efektivní úložiště dat na různých úrovních výkonu.

Azure Block BLOB Storage nabízí dvě různé úrovně výkonu:

- Premium: optimalizováno pro vysoké sazby za transakce a latence úložiště s jednou číslicí
- Standard: optimalizováno pro vysokou kapacitu a vysokou propustnost

Následující požadavky platí pro různé úrovně výkonu:

- Standardní výkon je k dispozici ve všech [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=storage). Výkon Premium je k dispozici ve [vybraných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=storage).
- Premium Performance poskytuje optimalizované ceny pro aplikace s vysokými nároky na transakce, které vám pomůžou [snížit celkové náklady na úložiště](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) pro tyto úlohy.
- Chcete-li získat špičkový výkon pro objekty blob bloku, je nutné použít typ účtu BlockBlobStorage.
- Standardní výkon je k dispozici pro Pro obecné účely V1, Pro obecné účely v2 a účty BLOB Storage.
- Výkon úrovně Premium a Standard podporují [objekty blob bloku s vysokou propustností](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/). Objekty blob bloku s vysokou propustností jsou k dispozici pro výkon Premium na více než 256 KiB. Objekty blob bloku s vysokou propustností jsou k dispozici pro standardní výkon ve více než 4 bloku umístění MiB nebo velikosti objektů BLOB.
- Špičkový výkon je momentálně dostupný jenom pro místně redundantní úložiště (LRS).

## <a name="premium-performance"></a>Výkon úrovně Premium

Úložiště objektů blob bloku úrovně Premium zpřístupňuje data prostřednictvím vysoce výkonného hardwaru. Data se ukládají na jednotky SSD (SSD), které jsou optimalizované pro nízkou latenci. SSD poskytují vyšší propustnost v porovnání s tradičními pevnými disky.

Úložiště objektů blob bloku Premium je ideální pro úlohy, které vyžadují rychlou a konzistentní dobu odezvy. Je nejvhodnější pro úlohy, které provádějí mnoho malých transakcí.

## <a name="standard-performance"></a>Standardní výkon

Standardní výkon podporuje různé [úrovně přístupu](storage-blob-storage-tiers.md) , aby bylo možné data ukládat nejefektivnějším způsobem. Je optimalizovaná pro vysokou kapacitu a vysokou propustnost u velkých datových sad.

## <a name="blob-lifecycle-management"></a>Správa životního cyklu objektů BLOB

Blob Storage Správa životního cyklu nabízí bohatou zásadu založenou na pravidlech:

- Data na konci svého životního cyklu Premium – vypršení platnosti
- Standardní – přechod dat na nejvyšší úroveň přístupu a vypršení platnosti dat na konci svého životního cyklu

Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md).

Data uložená v účtu úložiště blob bloku Premium se nedají přesouvat mezi horkou, studenou a archivní úrovní. Objekty blob můžete ale kopírovat z účtu bloku BLOB Storage do úrovně Hot Access v *jiném* účtu. K kopírování dat na jiný účet použijte [blok Put z URL](/rest/api/storageservices/put-block-from-url) API nebo [AzCopy v10 za účelem](../common/storage-use-azcopy-v10.md) . **Blok Put z** rozhraní API URL synchronně kopíruje data na serveru. Volání se dokončí až po přesunu všech dat z původního umístění serveru do cílového umístění.

## <a name="next-steps"></a>Další kroky

Vyhodnotit horkou, studenou a archivní v účtech GPv2 a BLOB Storage

- [Ověření dostupnosti horké, studené a archivní úrovně v jednotlivých oblastech](https://azure.microsoft.com/regions/#services)
- [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
- [Přečtěte si informace o prodehydratacích dat objektů BLOB z archivní úrovně.](storage-blob-rehydration.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../common/storage-enable-and-view-metrics.md)
- [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
- [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
