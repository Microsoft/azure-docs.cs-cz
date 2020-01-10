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
ms.openlocfilehash: 951d707c898ad0efa1f21480c12f0c733f5218ee
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834949"
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

<a id="api-scope-data-lake-client-library" />

## <a name="filesystem-support-in-sdks"></a>Podpora systému souborů v sadách SDK

- Podpora [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) a [Python](data-lake-storage-directory-file-acl-python.md) jsou ve verzi Public Preview. Jiné sady SDK se aktuálně nepodporují.
- Operace get a set ACL nejsou aktuálně rekurzivní.

## <a name="filesystem-support-in-powershell-and-azure-cli"></a>Podpora systému souborů v prostředí PowerShell a rozhraní příkazového řádku Azure

- Podpora [PowerShellu](data-lake-storage-directory-file-acl-powershell.md) a [Azure CLI](data-lake-storage-directory-file-acl-cli.md) je ve verzi Public Preview.
- Operace get a set ACL nejsou aktuálně rekurzivní.

## <a name="support-for-other-blob-storage-features"></a>Podpora dalších funkcí Blob Storage

Následující tabulka obsahuje seznam všech dalších funkcí a nástrojů, které ještě nejsou podporované nebo částečně podporované s účty úložiště, které mají hierarchický obor názvů (Azure Data Lake Storage Gen2).

| Funkce/nástroj    | Další informace    |
|--------|-----------|
| **Převzetí služeb při selhání účtu** |Zatím nepodporováno|
| **AzCopy** | Podpora specifická pro verzi <br><br>Použijte pouze nejnovější verzi AzCopy ([AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze AzCopy, jako je AzCopy v 8.1, nejsou podporovány.|
| **Zásady správy životního cyklu Azure Blob Storage** | Podporují se zásady správy životního cyklu (Preview).  Zaregistrujte se do verze Preview zásad správy životního cyklu a archivní úrovně přístupu [zde](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).   <br><br>Podporují se všechny úrovně přístupu. Úroveň přístupu archivu je momentálně ve verzi Preview. Odstranění snímků objektů BLOB ještě není podporováno.  V současné době existují nějaké chyby ovlivňující zásady správy životního cyklu a úroveň přístupu archivu.  |
| **Azure Content Delivery Network (CDN)** | Zatím nepodporováno|
| **Hledání Azure** |Podporováno (Preview)|
| **Azure Storage Explorer** | Podpora specifická pro verzi. <br><br>Používejte pouze verze `1.6.0` nebo vyšší. <br> V tuto chvíli existuje chyba úložiště ovlivňující `1.11.0` verze, která může v některých scénářích způsobit chyby ověřování. Zavádí se oprava chyby úložiště, ale jako alternativní řešení doporučujeme použít verzi `1.10.x`, která je k dispozici jako [bezplatné stahování](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` není ovlivněná chybou úložiště.|
| **Seznamy ACL kontejneru objektů BLOB** |Zatím nepodporováno|
| **Blobfuse** |Zatím nepodporováno|
| **Vlastní domény** |Zatím nepodporováno|
| **Průzkumník služby Storage v Azure Portal** | Omezená podpora. Seznamy řízení přístupu (ACL) ještě nejsou podporované. |
| **Protokolování diagnostiky** |Diagnostické protokoly jsou podporovány (Preview). <br><br>Průzkumník služby Azure Storage 1.10. x nelze použít pro zobrazení diagnostických protokolů. Pokud chcete zobrazit protokoly, použijte prosím AzCopy nebo sady SDK.
| **Neměnné úložiště** |Zatím nepodporováno <br><br>Neměnné úložiště poskytuje možnost ukládat data v [červech (jeden způsob zápisu, čtení mnoha)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) .|
| **Vrstvy na úrovni objektů** |Jsou podporovány studené a archivní úrovně. Archivní úroveň je ve verzi Preview. Všechny ostatní úrovně přístupu ještě nejsou podporované. <br><br> V současné době dochází k nějakým chybám, které mají vliv na úroveň přístupu archivu.  Zaregistrujte si verzi Preview úrovně přístupu archivu [tady](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VURjFLTDRGS0Q4VVZCRFY5MUVaTVJDTkROMi4u).|
| **Statické weby** |Zatím nepodporováno <br><br>Konkrétně možnost poskytovat soubory [statickým webům](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Aplikace třetích stran** | Omezená podpora <br><br>Aplikace třetích stran, které používají rozhraní REST API k práci, budou fungovat i v případě, že je použijete s Data Lake Storage Gen2. <br>Aplikace, které volají rozhraní API objektů blob, budou nejspíš fungovat.|
|**Obnovitelné odstranění** |Zatím nepodporováno|
| **Funkce správy verzí** |Zatím nepodporováno <br><br>Patří sem [obnovitelné odstranění](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)a další funkce pro správu verzí, jako jsou [snímky](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


