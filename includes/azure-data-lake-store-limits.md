---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665589"
---
**Azure Data Lake Storage Gen2** není vyhrazený typ služby nebo účtu úložiště. Je to nejnovější verze schopností, které jsou vyhrazeny pro analýzy velkých objemů dat.  Tyto možnosti jsou dostupné v účtu úložiště pro obecné účely v2 nebo BlockBlobStorage a můžete je získat tím, že v účtu povolíte funkci **hierarchického oboru názvů** . Cílení na škálování najdete v těchto článcích. 

- [Cíle škálování pro úložiště objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Cíle škálování pro účty úložiště úrovně Standard](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

**Azure Data Lake Storage Gen1** je vyhrazená služba. Pro úlohy analýzy velkých objemů dat je to škálovatelné úložiště na úrovni podniku. Můžete použít Data Lake Storage Gen1 k zachycení dat libovolné velikosti, typu a rychlosti příjmu na jednom místě pro provozní a průzkumné analýzy. Množství dat, která můžete ukládat v účtu Data Lake Storage Gen1, není nijak omezené.

| **Prostředek** | **Počtu** | **Komentáře** |
| --- | --- | --- |
| Maximální počet Data Lake Storage Gen1 účtů na předplatné v jednotlivých oblastech |10 | Pokud chcete požádat o zvýšení tohoto limitu, obraťte se na podporu. |
| Maximální počet přístupových seznamů ACL na jeden soubor nebo složku |32 | Toto je pevný limit. Skupiny můžete použít ke správě přístupu s menším počtem položek. |
| Maximální počet výchozích seznamů řízení přístupu (ACL) na jeden soubor nebo složku |32 | Toto je pevný limit. Skupiny můžete použít ke správě přístupu s menším počtem položek. |
