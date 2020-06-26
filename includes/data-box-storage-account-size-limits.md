---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378537"
---
Tady jsou limity velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. Nejaktuálnější informace o těchto omezeních najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../articles/storage/blobs/scalability-targets.md) a [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu služby Azure Storage                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | Maximální limit je stejný jako [limit úložiště definovaný pro předplatné Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) a zahrnuje data ze všech zdrojů, včetně data box.|
| Soubory Azure                                                          | Maximální velikost standardních sdílených souborů je 5 TB. <br> Maximální velikost sdílených složek na úrovni Premium je 100TiB na sdílenou složku.<br> Všechny složky v *StorageAccount_AzureFiles* musí splňovat toto omezení.       |
