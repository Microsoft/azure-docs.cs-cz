---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736973"
---
Tady jsou limity velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. Nejaktuálnější informace o těchto omezeních najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../articles/storage/blobs/scalability-targets.md) a [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu služby Azure Storage                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | 2 PB pro USA a Evropu.<br>500 TB pro všechny ostatní oblasti, které zahrnují Velká Británie.  <br> To zahrnuje data ze všech zdrojů, včetně Data Box.|
| Soubory Azure                                                          | Maximální velikost standardních sdílených složek 100TiB *, 5 TB, Premium 100TiB pro sdílení souborů na sdílenou složku.<br> Všechny složky v *StorageAccount_AzureFiles* musí splňovat toto omezení.       |
