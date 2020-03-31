---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75467529"
---
Tady jsou omezení velikosti dat, která se zkopírují do účtu úložiště. Ujistěte se, že data, která nahrajete, odpovídají těmto limitům. Nejaktuálnější informace o těchto omezeních najdete v tématu [Škálovatelnost a cíle výkonu pro úložiště objektů Blob](../articles/storage/blobs/scalability-targets.md) a [cíle škálovatelnosti a výkonu souborů Azure](../articles/storage/files/storage-files-scale-targets.md).

| Velikost dat zkopírovaných do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objekt blob stránky                                            | 2 PB pro USA a Evropu.<br>500 TB pro všechny ostatní regiony, které zahrnují Spojené království.  <br> To zahrnuje data ze všech zdrojů včetně datové schránky.|
| Soubory Azure                                                          | 5 TB na akcii.<br> Všechny složky pod *StorageAccount_AzureFiles* musí dodržovat toto omezení.       |
