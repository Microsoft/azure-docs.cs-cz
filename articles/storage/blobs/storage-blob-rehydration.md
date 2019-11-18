---
title: Dehydratované data objektů BLOB z archivní úrovně
description: Pokud chcete získat přístup k datům, rehydratované objekty BLOB z archivního úložiště.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: d6370509b49ae464b53525e7320676b04912bd12
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113706"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dehydratované data objektů BLOB z archivní úrovně

Když je objekt BLOB v archivní úrovni, považuje se za offline a nedá se číst ani upravovat. Metadata objektu BLOB zůstávají v online a k dispozici, což vám umožní zobrazit seznam objektů BLOB a jejích vlastností. Čtení a úpravy dat objektů BLOB jsou k dispozici pouze u online vrstev, jako je horká nebo studená. K dispozici jsou dvě možnosti načtení a přístup k datům uloženým v archivní úrovni přístupu.

1. [Dehydratované archivovaný objekt blob do online úrovně](#rehydrate-an-archived-blob-to-an-online-tier) – dehydratované objekt BLOB archivu na horkou nebo studenou změnou jeho úrovně pomocí operace [nastavit vrstvu objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Zkopírování archivovaného objektu blob do online úrovně](#copy-an-archived-blob-to-an-online-tier) – vytvoří novou kopii objektu BLOB archivu pomocí operace [kopírování objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Zadejte jiný název objektu BLOB a cílovou úroveň horké nebo studené.

 Další informace o úrovních najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Dehydratované archivovaný objekt blob do online úrovně

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Kopírování archivovaného objektu blob do online úrovně

Pokud nechcete znovu vyměnit svůj archivní objekt blob, můžete zvolit operaci [kopírování objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Původní objekt BLOB zůstane v archivu beze změny, zatímco nový objekt BLOB se vytvoří v online horké nebo studené vrstvě, kde můžete pracovat. V operaci kopírování objektu blob můžete také nastavit volitelnou vlastnost *x-MS-rehydratované priority* na úroveň Standard nebo high (Preview) a určit prioritu, na které chcete vytvořit kopii objektu BLOB.

Archivní objekty BLOB se dají zkopírovat jenom do online cílových vrstev v rámci stejného účtu úložiště. Kopírování objektu BLOB archivu do jiného archivní objektu BLOB se nepodporuje.

Kopírování objektu BLOB z archivu může trvat hodiny na dokončení v závislosti na vybrané prioritě rehydratovaného. Na pozadí operace **kopírování objektu BLOB** přečte váš zdrojový objekt BLOB archivu a vytvoří nový objekt BLOB online ve vybrané cílové vrstvě. Nový objekt BLOB může být viditelný při výpisu objektů blob, ale data nejsou dostupná, dokud se nedokončí čtení ze zdrojového objektu BLOB archivu a data se zapisují do nového online cílového objektu BLOB. Nový objekt BLOB je jako nezávislá kopie a jakákoli změna nebo odstranění do něj nemá vliv na zdrojový objekt BLOB archivu.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Dosazování objektů BLOB z archivu do horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Použití funkce s vysokou prioritou (Preview) má vyšší náklady na operace a načítání dat ve srovnání se standardní prioritou. Dosazování s vysokou prioritou se ve vašem vyúčtování zobrazí jako samostatná položka řádku. Pokud žádost s vysokou prioritou vrátí objekt BLOB pro archivaci o několika gigabajtech, bude trvat víc než 5 hodin, bude se vám účtovat frekvence načítání s vysokou prioritou. Nicméně standardní míry načítání se ještě použijí, protože pro vyvýšení se nacházelo upřednostnění přes jiné požadavky.

Kopírování objektů BLOB z archivu na horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Pro vytvoření nové kopie objektu BLOB se účtuje operace zápisu. Poplatky za předčasné odstranění se nepoužívají při kopírování do online objektu blob, protože zdrojový objekt BLOB zůstane v archivní úrovni nezměněný. Poplatky za načtení s vysokou prioritou se použijí, pokud je vybraná možnost.

Objekty BLOB v archivní úrovni by měly být uložené minimálně 180 dnů. Odstranění nebo dehydratovanéní archivovaných objektů BLOB před 180 dny bude platit poplatky za předčasné odstranění.

> [!NOTE]
> Další informace o cenách pro objekty blob bloku a data pro dehydrataci najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/blobs/). Další informace o poplatcích za odchozí přenosy dat najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/data-transfers/)přenosů dat.

## <a name="next-steps"></a>Další kroky

* [Další informace o Blob Storagech vrstvách](storage-blob-storage-tiers.md)
* [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
* [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
* [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
