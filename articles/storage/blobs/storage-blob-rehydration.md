---
title: Dehydratované data objektů BLOB z archivní úrovně
description: Pokud chcete získat přístup k datům, rehydratované objekty BLOB z archivního úložiště.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958210"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dehydratované data objektů BLOB z archivní úrovně

Když je objekt BLOB v archivní úrovni, považuje se za offline a nedá se číst ani upravovat. Metadata objektu BLOB zůstávají v online a k dispozici, což vám umožní zobrazit seznam objektů BLOB a jejích vlastností. Čtení a úpravy dat objektů BLOB jsou k dispozici pouze u online vrstev, jako je horká nebo studená. K dispozici jsou dvě možnosti načtení a přístup k datům uloženým v archivní úrovni přístupu.

1. [Dehydratované archivovaný objekt blob do online úrovně](#rehydrate-an-archived-blob-to-an-online-tier) – dehydratované archivovaný objekt blob na horkou nebo studenou změnou jeho úrovně pomocí operace [nastavit úroveň objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Kopírování archivovaného objektu blob do online úrovně](#copy-an-archived-blob-to-an-online-tier) – vytvoří novou kopii archivovaného objektu BLOB pomocí operace [kopírování objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Zadejte jiný název objektu BLOB a cílovou úroveň horké nebo studené.

 Další informace o úrovních najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Dehydratované archivovaný objekt blob do online úrovně

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopírování archivovaného objektu blob do online úrovně

Pokud nechcete dehydratované objekt blob, můžete zvolit operaci [kopírování objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Původní objekt BLOB zůstane v archivu při práci na novém objektu BLOB v horké nebo studené vrstvě beze změny. Při použití procesu kopírování můžete nastavit volitelnou vlastnost *x-MS-rehydratované priority* na Standard nebo high (Preview).

Archivní objekty BLOB se dají zkopírovat jenom do online cílových vrstev. Kopírování objektu BLOB archivu do jiného archivní objektu BLOB se nepodporuje.

Kopírování objektu BLOB z archivu trvá čas. Na pozadí operace **kopírování objektu BLOB** dočasně rehydratované zdrojový objekt BLOB archivu, aby bylo možné vytvořit nový objekt BLOB online v cílové vrstvě. Tento nový objekt BLOB není k dispozici, dokud nebude dokončeno dočasné dosazování z archivu a data jsou zapsána do nového objektu BLOB.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Dosazování objektů BLOB z archivu do horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Použití funkce s vysokou prioritou (Preview) má vyšší náklady na operace a načítání dat ve srovnání se standardní prioritou. Vyúčtování s vysokou prioritou se ve vašem vyúčtování zobrazí jako samostatná položka řádku. Pokud je požadavek s vysokou prioritou, který vrátí objekt BLOB pro archivaci několika gigabajtů, trvá více než 5 hodin, bude se vám účtovat frekvence načítání s vysokou prioritou. Nicméně stále platí standardní míry načítání.

Kopírování objektů BLOB z archivu na horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Pro vytvoření nové kopie se účtuje operace zápisu. Poplatky za předčasné odstranění se nepoužívají při kopírování do online objektu blob, protože zdrojový objekt BLOB zůstane v archivní úrovni nezměněný. Platí se pro ně poplatky s vysokou prioritou.

Objekty BLOB v archivní úrovni by měly být uložené minimálně 180 dnů. Odstranění nebo dehydratovanéní archivovaných objektů BLOB před 180 dny bude platit poplatky za předčasné odstranění.

> [!NOTE]
> Další informace o cenách pro objekty blob bloku a data pro dehydrataci najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/blobs/). Další informace o poplatcích za odchozí přenosy dat najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/data-transfers/)přenosů dat.

## <a name="next-steps"></a>Další kroky

* [Další informace o Blob Storagech vrstvách](storage-blob-storage-tiers.md)
* [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
* [Správa životního cyklu služby Azure Blob Storage](storage-lifecycle-management-concepts.md)
* [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
