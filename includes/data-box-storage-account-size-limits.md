---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555950"
---
Tady jsou limity velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. Nejaktuálnější informace o těchto omezeních najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../articles/storage/blobs/scalability-targets.md) a [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu služby Azure Storage                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | Maximální limit je stejný jako [limit úložiště definovaný pro předplatné Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) a zahrnuje data ze všech zdrojů, včetně data box. |
| Soubory Azure                                                          | Data Box podporuje velké sdílené složky (100TiB), pokud je povoleno před vytvořením Data Box objednávky. <br> Pokud není povolený před vytvořením objednávky, maximální podporovaná velikost sdílení souborů je 5 TiB. <br> Soubory úrovně Premium se zatím nepodporují.<br> Všechny složky v *StorageAccount_AzureFiles* musí splňovat toto omezení. <br> Další informace najdete v tématu [povolení a vytváření velkých sdílených složek](../articles/storage/files/storage-files-how-to-create-large-file-share.md) .      |