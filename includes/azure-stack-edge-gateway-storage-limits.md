---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 1c6a3af7263cb4f1363425aa137fff04a6ad5120
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466762"
---
Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Azure Stack Edge. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují omezení služby Azure Storage, nebo nevyhovují zásadám vytváření názvů souborů Azure nebo objektů blob, pak se tyto soubory nebo adresáře ingestují do Azure Storage prostřednictvím služby Azure Stack Edge.