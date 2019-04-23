---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: d56fb411eb032e5e6227d68cd8abe02c0e30850b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006858"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek obsahuje známé problémy a omezení s Data Lake Storage Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Podpora v sadě SDK pro účty Data Lake Storage Gen2

Nejsou k dispozici sady SDK jsou dostupné, které budou fungovat s účty Data Lake Storage Gen2.

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

Zatím nejsou k dispozici do účtů Data Lake Storage Gen2 úložiště objektů BLOB rozhraní API.

Tato rozhraní API je zakázané, aby zabránilo problémům s přístup zvyšuje ochranu před nechtěnými daty, které mohou nastat, protože zatím nejsou spolupracují se službou Azure Data Lake Gen2 API rozhraní API služby Blob Storage.

Pokud jste použili tato rozhraní API k načtení dat, než byly zakázány, aby bylo produkční požadavek na přístup k datům, obraťte se Microsoft Support s následujícími informacemi:

* ID předplatného (identifikátor GUID, nikoli název)

* Názvy účtů úložišť

* Zda se aktivně to týká v produkčním prostředí a pokud ano, pro které účty úložiště?

* I když nejsou aktivně vliv v produkčním prostředí, dejte nám vědět, jestli tato data, které se mají zkopírovat do jiného účtu úložiště z nějakého důvodu potřebujete a pokud ano, proč?

Za těchto okolností abychom mohli obnovit přístup k rozhraní API objektů Blob po omezenou dobu tak, aby tato data můžete zkopírovat do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

Nespravované disky virtuálních počítačů (VM) závisí na zakázaném rozhraní API úložiště objektů Blob, takže pokud chcete povolit hierarchického oboru názvů na účet úložiště, vezměte v úvahu umístění nespravované disky virtuálních počítačů do účtu úložiště, který nemá funkci hierarchického oboru názvů povolené.

## <a name="api-interoperability"></a>Vzájemná funkční spolupráce rozhraní API

Rozhraní API služby BLOB Storage a Azure Data Lake Gen2 API nejsou interoperabilní mezi sebou.

Pokud máte nástroje, aplikace, služby nebo skripty, které používají rozhraní API služby Blob, a chcete použít pro práci se veškerý obsah, který nahrajete do vašeho účtu, potom nepovolí hierarchického oboru názvů na svůj účet Blob storage než stane rozhraní API služby Blob vzájemná spolupráce s Gen2 API služby Azure Data Lake. Použití účtu úložiště bez hierarchického oboru názvů znamená, že pak nebudete mít přístup k Data Lake Storage Gen2 specifické funkce, jako je například adresář a soubor seznamy řízení přístupu k systému.

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

K zobrazení nebo Správa účtů Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage, potřebujete alespoň verzi `1.6.0` nástroje, která je k dispozici jako [zdarma ke stažení](https://azure.microsoft.com/features/storage-explorer/).

Všimněte si, že verze Průzkumníka služby Storage, který je vložen do portálu Azure portal se aktuálně podporují zobrazení a Správa účtů Data Lake Storage Gen2 s povolenou funkci hierarchického oboru názvů.

## <a name="blob-viewing-tool"></a>Nástroj pro zobrazení objektů BLOB

Nástroje pro zobrazení na webu Azure portal objekt BLOB má omezenou podporu pro Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Aplikace třetí strany

Data Lake Storage Gen2 nemusí podporovat aplikace třetích stran.

Podpora je uvážení jednotlivých poskytovatelů aplikací třetích stran. V současné době služba Blob storage API a rozhraní API pro Data Lake Storage Gen2 nelze použít ke správě stejný obsah. Jak pracujeme, abychom zajistili interoperabilitu tohoto, je možné, že mnoho nástrojů třetích stran automaticky podporují Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Podpora nástroje AzCopy

AzCopy verze 8 nepodporuje Data Lake Storage Gen2.

Místo toho použijte nejnovější verzi AzCopy ve verzi preview ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) podporuje Data Lake Storage Gen2 koncových bodů.

## <a name="azure-event-grid"></a>Azure Event Grid

[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) neobdrží události z účtů Azure Data Lake Gen2, protože tyto účty nejsou zatím je vygenerovat.  

## <a name="soft-delete-and-snapshots"></a>Obnovitelného odstranění a snímky

Nejsou k dispozici pro účty Data Lake Storage Gen2 obnovitelného odstranění a snímků.

Všechny funkce správy verzí, včetně [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) a [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) zatím nejsou k dispozici pro účty úložiště, které mají povolenou funkci hierarchického oboru názvů.

## <a name="object-level-storage-tiers"></a>Vrstvy úložiště na úrovni objektu

Objekt vrstvy úrovně úložiště (Hot, studené a archivní) zatím nejsou k dispozici pro účty Azure Data Lake Storage Gen 2, ale jsou dostupné pro účty úložiště, které nemají povolenou funkci hierarchického oboru názvů.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Zásady správy životního cyklu Azure Blob Storage

Zatím nejsou k dispozici pro účty Data Lake Storage Gen2 zásady správy životního cyklu Azure Blob Storage.

Tyto zásady jsou dostupné pro účty úložiště, které nemají povolenou funkci hierarchického oboru názvů.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Diagnostické protokoly nejsou k dispozici pro účty Data Lake Storage Gen2.
