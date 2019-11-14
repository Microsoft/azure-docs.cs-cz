---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Další informace o omezeních a známých problémech s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033945"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

V tomto článku jsou uvedené funkce a nástroje, které se ještě nepodporují, nebo jenom částečně podporované s účty úložiště, které mají hierarchický obor názvů (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problémy a omezení s použitím rozhraní API objektů BLOB

Rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 můžou pracovat se stejnými daty.

Tato část popisuje problémy a omezení s použitím rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 pro práci se stejnými daty.

* Rozhraní API objektů BLOB a rozhraní Data Lake Storage API nemůžete použít k zápisu do stejné instance souboru. Pokud zapisujete do souboru pomocí Data Lake Storage Gen2 rozhraní API, pak bloky tohoto souboru nebudou viditelné pro volání rozhraní API objektů BLOB [Get Block](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Soubor můžete přepsat buď pomocí rozhraní API Data Lake Storage Gen2 nebo rozhraní API objektů BLOB. To nebude mít vliv na vlastnosti souboru.

* Když použijete operaci [listovat BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez zadání oddělovače, výsledky budou zahrnovat adresáře a objekty blob. Pokud se rozhodnete použít oddělovač, použijte pouze lomítko (`/`). Toto je jediný podporovaný oddělovač.

* Použijete-li k odstranění adresáře rozhraní API pro [odstranění objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) , bude tento adresář odstraněn pouze v případě, že je prázdný. To znamená, že nemůžete rekurzivně odstraňovat adresáře pomocí rozhraní BLOB API.

Tato rozhraní REST API pro objekty blob nejsou podporovaná:

* [Vložit objekt BLOB (stránka)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Vložit stránku](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Získat rozsahy stránek](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Objekt BLOB přírůstkového kopírování](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Vložit stránku z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Vložit objekt BLOB (připojit)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Připojit blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Připojit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Nespravované disky virtuálních počítačů nejsou podporované v účtech, které mají hierarchický obor názvů. Pokud chcete povolit hierarchický obor názvů v účtu úložiště, umístěte nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

## <a name="support-for-other-blob-storage-features"></a>Podpora dalších funkcí Blob Storage

Následující tabulka obsahuje seznam všech dalších funkcí a nástrojů, které ještě nejsou podporované nebo částečně podporované s účty úložiště, které mají hierarchický obor názvů (Azure Data Lake Storage Gen2).

| Funkce/nástroj    | Další informace    |
|--------|-----------|
| **Rozhraní API pro Data Lake Storage Gen2** | Částečně podporováno <br><br>V aktuální verzi můžete použít Data Lake Storage Gen2 rozhraní **REST** API k interakci s adresáři a nastavení seznamů řízení přístupu (ACL), ale k provedení těchto úloh nejsou k dispozici žádné jiné sady SDK (například .NET, Java nebo Python). Chcete-li provádět další úkoly, jako je například nahrávání a stahování souborů, můžete použít sady SDK objektů BLOB.  |
| **AzCopy** | Podpora specifická pro verzi <br><br>Použijte pouze nejnovější verzi AzCopy ([AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze AzCopy, jako je AzCopy v 8.1, nejsou podporovány.|
| **Zásady správy životního cyklu Azure Blob Storage** | Podporují se všechny úrovně přístupu. Úroveň přístupu archivu je momentálně ve verzi Preview. Odstranění snímků objektů BLOB ještě není podporováno. |
| **Azure Content Delivery Network (CDN)** | Zatím nepodporováno|
| **Hledání Azure** |Podporováno (Preview)|
| **Azure Storage Explorer** | Podpora specifická pro verzi <br><br>Pomocí `1.10.0``1.6.0` pouze verze. <br> Verze `1.10.0` je k dispozici [zdarma ke stažení](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). Verze `1.11.0` ještě není podporovaná.|
| **Seznamy ACL kontejneru objektů BLOB** |Zatím nepodporováno|
| **Blobfuse** |Zatím nepodporováno|
| **Vlastní domény** |Zatím nepodporováno|
| **Průzkumník služby Storage v Azure Portal** | Omezená podpora. Seznamy řízení přístupu (ACL) ještě nejsou podporované. |
| **Protokolování diagnostiky** |Diagnostické protokoly jsou podporovány (Preview).<br><br>Povolení protokolů v Azure Portal není aktuálně podporováno. Tady je příklad, jak povolit protokoly pomocí PowerShellu. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Nezapomeňte zadat `Blob` jako hodnotu parametru `-ServiceType`, jak je znázorněno v tomto příkladu. <br><br>V současné době nelze Průzkumník služby Azure Storage použít pro zobrazení diagnostických protokolů. Pokud chcete zobrazit protokoly, použijte prosím AzCopy nebo sady SDK.
| **Neměnné úložiště** |Zatím nepodporováno <br><br>Neměnné úložiště poskytuje možnost ukládat data v [červech (jeden způsob zápisu, čtení mnoha)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Vrstvy na úrovni objektů** |Jsou podporovány studené a archivní úrovně. Archivní úroveň je ve verzi Preview. Všechny ostatní úrovně přístupu ještě nejsou podporované.|
| **Podpora PowerShellu a rozhraní příkazového řádku** | Omezená funkčnost <br><br>Jsou podporovány operace objektů BLOB. Práce s adresáři a nastavování seznamů řízení přístupu (ACL) ještě není podporovaná. |
| **Statické weby** |Zatím nepodporováno <br><br>Konkrétně možnost poskytovat soubory [statickým webům](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikace třetích stran** | Omezená podpora <br><br>Aplikace třetích stran, které používají rozhraní REST API k práci, budou fungovat i v případě, že je použijete s Data Lake Storage Gen2. <br>Aplikace, které volají rozhraní API objektů blob, budou nejspíš fungovat.|
|**Obnovitelné odstranění** |Zatím nepodporováno|
| **Funkce správy verzí** |Zatím nepodporováno <br><br>Patří sem [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)a další funkce pro správu verzí, jako jsou [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


