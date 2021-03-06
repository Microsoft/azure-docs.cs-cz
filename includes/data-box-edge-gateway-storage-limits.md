---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028100"
---
Tato část popisuje omezení pro službu Azure Storage Service a požadované zásady vytváření názvů pro soubory Azure, objekty blob bloku Azure a objekty blob stránky Azure, které se vztahují na službu Azure Stack Edge/Data Box Gateway. Pečlivě si prohlédněte omezení úložiště a sledujte všechna doporučení.

Nejnovější informace o omezeních služby Azure Storage a osvědčených postupech pro pojmenování sdílených složek, kontejnerů a souborů najdete tady:

- [Pojmenování kontejnerů a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Zásady objektů blob bloku a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud nějaké soubory nebo adresáře překračují limity služby Azure Storage nebo neodpovídají zásadám vytváření názvů souborů nebo objektů blob v Azure, tyto soubory nebo adresáře se prostřednictvím služby Azure Stack Edge nebo Data Box Gateway neingestují do služby Azure Storage.