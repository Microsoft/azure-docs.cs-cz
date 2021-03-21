---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: d12a5042d0c2d79989d82e86e7265d030912f5ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900846"
---
Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Azure Stack Edge. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují omezení služby Azure Storage, nebo nevyhovují zásadám vytváření názvů souborů Azure nebo objektů blob, pak se tyto soubory nebo adresáře ingestují do Azure Storage prostřednictvím služby Azure Stack Edge.