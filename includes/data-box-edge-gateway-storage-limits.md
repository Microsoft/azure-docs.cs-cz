---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175463"
---
Tato část popisuje omezení pro službu Azure Storage a požadované zásady vytváření názvů pro soubory Azure, objekty BLOB bloku Azure a objekty BLOB stránky Azure, případně ke službě Data Box brány/Data pole Edge. Pečlivě zkontrolujte omezení úložiště a postupujte podle všech doporučení.

Nejnovější informace o omezení služby Azure storage a osvědčené postupy pro zadávání názvů sdílených složek, kontejnery a souborů přejděte na:

- [Pojmenování a odkazování na ně kontejnery](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Pojmenování sdílených složek a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Objekty BLOB bloku a vytváření názvů objektů blob stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Pokud jsou všechny soubory a adresáře, které překračují omezení služby Azure Storage, nebo není v souladu s zásady vytváření názvů souborů a objektů Blob v Azure, pak tyto soubory nebo adresáře se ingestuje do služby Azure Storage pomocí služby Data Box brány/Data pole Edge.