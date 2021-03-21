---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 624df1185f86004b48bcca921e76d55bfb14c48d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98900838"
---
Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Azure Stack Edge/Data Box Gateway. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud nějaké soubory nebo adresáře překračují limity služby Azure Storage nebo neodpovídají zásadám vytváření názvů souborů nebo objektů blob v Azure, tyto soubory nebo adresáře se prostřednictvím služby Azure Stack Edge nebo Data Box Gateway neingestují do služby Azure Storage.