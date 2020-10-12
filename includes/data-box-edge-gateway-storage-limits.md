---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 51fde73565c9c4cdb21e998b8d43d303ceef062e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561395"
---
Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Azure Stack Edge/Data Box Gateway. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud existují nějaké soubory nebo adresáře, které překračují omezení služby Azure Storage, nebo nevyhovují zásadám vytváření názvů souborů Azure nebo objektů blob, pak se tyto soubory nebo adresáře ingestují do Azure Storage přes Azure Stack hraniční/data box Gateway služba.