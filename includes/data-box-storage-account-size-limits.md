---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467529"
---
Tady jsou limity velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahráváte, jsou v souladu s těmito omezeními. Nejaktuálnější informace o těchto omezeních najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../articles/storage/blobs/scalability-targets.md) a [škálovatelnost a cíle výkonnosti souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu služby Azure Storage                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | 2 PB pro USA a Evropu.<br>500 TB pro všechny ostatní oblasti, které zahrnují Velká Británie.  <br> To zahrnuje data ze všech zdrojů, včetně Data Box.|
| Azure Files                                                          | 5 TB na sdílenou složku.<br> Všechny složky v *StorageAccount_AzureFiles* musí splňovat toto omezení.       |
