---
title: Známé problémy s Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Přečtěte si o omezeních a známých problémech Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919607"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek popisuje omezení a známé problémy Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Podporované funkce služby Blob Storage

Rostoucí počet funkcí služby Blob Storage teď funguje s účty, které mají hierarchický obor názvů. Úplný seznam najdete [v tématu BLOB Storage funkce dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Podívejte [se na služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Několik opensourcové platformy podporují Data Lake Storage Gen2. Úplný seznam najdete v tématu [Open Source Platforms, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Viz [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Úložiště objektů BLOB rozhraní API

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

## <a name="file-system-support-in-sdks"></a>Podpora systému souborů v sadách SDK

- [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) a [Python](data-lake-storage-directory-file-acl-python.md)a [JavaScript](data-lake-storage-directory-file-acl-javascript.md) a podpora jsou ve verzi Public Preview. Jiné sady SDK se aktuálně nepodporují.
- Operace get a set ACL nejsou aktuálně rekurzivní.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Podpora systému souborů v prostředí PowerShell a rozhraní příkazového řádku Azure

- Podpora [PowerShellu](data-lake-storage-directory-file-acl-powershell.md) a [Azure CLI](data-lake-storage-directory-file-acl-cli.md) je ve verzi Public Preview.
- Operace get a set ACL nejsou aktuálně rekurzivní.

## <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

* Odstranění snímků objektů BLOB ještě není podporováno.  

* V současné době existují nějaké chyby ovlivňující zásady správy životního cyklu a úroveň přístupu archivu. 

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Průzkumník služby Azure Storage 1.10. x nelze použít pro zobrazení diagnostických protokolů. Pokud chcete zobrazit protokoly, použijte prosím AzCopy nebo sady SDK.

## <a name="blobfuse"></a>Blobfuse

Blobfuse se nepodporuje.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Použijte pouze nejnovější verzi AzCopy ([AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze AzCopy, jako je AzCopy v 8.1, nejsou podporovány.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Používejte pouze verze `1.6.0` nebo vyšší. V tuto chvíli existuje chyba úložiště ovlivňující `1.11.0`verze  , která může v některých scénářích způsobit chyby ověřování. Zavádí se oprava chyby úložiště, ale jako alternativní řešení doporučujeme použít verzi `1.10.x` , která je k dispozici jako [bezplatné stahování](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` není ovlivněná chybou úložiště.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Průzkumník služby Storage na webu Azure Portal

Seznamy řízení přístupu (ACL) ještě nejsou podporované.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplikace třetích stran

Aplikace třetích stran, které používají rozhraní REST API k práci, budou fungovat i v případě, že je použijete s Data Lake Storage Gen2 aplikacemi, které volají rozhraní API objektů blob, budou pravděpodobně fungovat.





