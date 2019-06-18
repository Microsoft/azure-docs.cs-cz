---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 7b46360886387bc4ab48f764edb5d01dc52dc812
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244561"
---
Tady jsou omezení velikosti dat, která je zkopírován do účtu úložiště. Ujistěte se, že data, která nahrajete odpovídá tato omezení. Nejaktuálnější informace o těchto omezeních najdete v části [cíle škálování Azure blob storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) a [soubory Azure škálovat cíle](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Velikost dat zkopírována do účtu úložiště Azure                      | Výchozí omezení          |
|---------------------------------------------------------------------|------------------------|
| Objekt blob bloku a objektů blob stránky                                            | 500 TB na jeden účet úložiště. <br> To zahrnuje data ze všech zdrojů, včetně zařízení Data Box.|
| Azure File                                                          | 5 TiB jednotlivou sdílenou složku.<br> Všechny složky s *StorageAccount_AzureFiles* musí postupovat podle tohoto limitu.       |
