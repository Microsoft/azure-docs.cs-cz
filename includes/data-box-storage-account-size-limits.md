---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225412"
---
Tady jsou limity velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. Nejaktuálnější informace o těchto omezeních najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../articles/storage/blobs/scalability-targets.md) a [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu služby Azure Storage                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | Maximální limit je stejný jako [limit úložiště definovaný pro předplatné Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) a zahrnuje data ze všech zdrojů, včetně data box.   |
| Azure Files                                                          | Data Box podporuje velké sdílené složky (100 TiB), pokud je povolený před vytvořením Data Box objednávky. <br> Pokud není povolený před vytvořením objednávky, maximální podporovaná velikost sdílení souborů je 5 TiB. <br> Data Box podporuje sdílené složky Azure Premium, které umožňují celkem 100 TiB pro všechny sdílené složky v účtu úložiště. <br> Maximální použitelná kapacita je mírně menší z důvodu místa, které kopíruje protokoly a protokoly auditu. Minimálně 100 GiB jsou rezervované pro protokol kopírování a protokol auditu. Další informace najdete v tématu [protokoly auditu pro Azure Data Box Azure Data box Heavy](../articles/databox/data-box-audit-logs.md). <br> Všechny složky v *StorageAccount_AzureFiles* musí splňovat toto omezení. <br> Další informace najdete v tématu [povolení a vytváření velkých sdílených složek](../articles/storage/files/storage-files-how-to-create-large-file-share.md) .      |
