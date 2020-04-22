---
title: Známé problémy s Úložištěm datových jezer Azure Gen2 | Dokumenty společnosti Microsoft
description: Přečtěte si o omezeních a známých problémech Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: dfa4d65464192b90d4a6f74255faaf8b664ce118
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767976"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek popisuje omezení a známé problémy Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

Rostoucí počet funkcí úložiště objektů Blob teď pracuje s účty, které mají hierarchický obor názvů. Úplný seznam najdete v tématu [funkce úložiště objektů blob dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Azure Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuálních reprezentací. Seznam podporovaných služeb Azure najdete v [tématu služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Podívejte se na [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Několik open source platforem podporuje Data Lake Storage Gen2. Úplný seznam najdete [v tématu Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Viz [Open source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Api úložiště objektů blob

Data API objektů blob a data lake storage gen2 api můžete pracovat na stejná data.

Tato část popisuje problémy a omezení s použitím objektů BLOB API a data Lake Storage Gen2 API pro provoz na stejná data.

* K zápisu do stejné instance souboru nelze použít api pro objekty BLOB i úložiště datových jezer. Pokud zapisujete do souboru pomocí rozhraní API Data Lake Storage Gen2, bloky tohoto souboru nebudou viditelné pro volání rozhraní BLOB [Get Block List.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Soubor můžete přepsat pomocí api úložiště datového jezera Gen2 nebo objektů API objektů blob. To neovlivní vlastnosti souboru.

* Při použití [operace seznam objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez zadání oddělovače, výsledky budou zahrnovat adresáře a objekty BLOB. Pokud se rozhodnete použít oddělovač, použijte pouze`/`lomítko ( ). Toto je jediný podporovaný oddělovač.

* Pokud k odstranění adresáře použijete rozhraní [DELETE Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API, bude tento adresář odstraněn pouze v případě, že je prázdný. To znamená, že nelze použít adresáře odstranění objektů Blob API rekurzivně.

Tato api objektů BLOB REST nejsou podporována:

* [Umístit objekt blob (stránka)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Umístit stránku](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Získat rozsahy stránek](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Objekt blob přírůstkové kopie](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Umístit stránku z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Umístit objekt blob (připojit)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Připojit blok](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Připojit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Nespravované disky virtuálních počítačů nejsou podporovány v účtech, které mají hierarchický obor názvů. Pokud chcete povolit hierarchický obor názvů v účtu úložiště, umístěte nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Podpora systému souborů v sadách SDK, PowerShellu a Azure CLI

- Získat a nastavit operace acl nejsou aktuálně rekurzivní.
- [Podpora vykreslování klišé Azure](data-lake-storage-directory-file-acl-cli.md) je ve verzi Public Preview.


## <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

* Odstranění snímků objektů blob ještě není podporováno.  

## <a name="archive-tier"></a>Archivní úroveň

V současné době existuje chyba, která ovlivňuje úroveň přístupu archivu.


## <a name="blobfuse"></a>Blůza

Blobfuse není podporována.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Používejte pouze nejnovější verzi AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Dřívější verze AzCopy, například AzCopy v8.1, nejsou podporovány.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Používejte pouze `1.6.0` verze nebo vyšší.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Průzkumník úložiště na webu Azure Portal

Avitální prodejny a dosud nejsou podporovány.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Žádosti třetích stran

Aplikace třetích stran, které používají REST API pro práci bude i nadále fungovat, pokud je používáte s Data Lake Storage Gen2 Aplikace, které volají objektová bbulapi bude pravděpodobně fungovat.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Seznamy řízení přístupu (ACL) a anonymní přístup ke čtení

Pokud [anonymní přístup pro čtení](storage-manage-access-to-resources.md) byla udělena kontejneru, pak seznamy ACNemají žádný vliv na tento kontejner nebo soubory v tomto kontejneru.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Ovladač objektu blob úložiště Windows Azure (WASB) (bez podpory s datovým jezerem Gen2)

V současné době ovladač WASB, který byl navržen pro práci pouze s rozhraním BLOB API, dochází k problémům v několika běžných scénářích. Konkrétně pokud je klientem účtu úložiště s povolenou hierarchickou službou namespace. Přístup k více protokolům v úložišti datového jezera tyto problémy nezmírní. 

V současné době (a s největší pravděpodobností v dohledné budoucnosti) nebudeme podporovat zákazníky, kteří používají ovladač WASB jako klienta k účtu úložiště s povolenou hierarchickou jmennou službou. Místo toho doporučujeme, abyste se rozhodli použít ovladač [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) v prostředí Hadoop. Pokud se pokoušíte migrovat z místního prostředí Hadoop s verzí starší než Pobočka Hadoop-3, otevřete lístek podpory Azure, abychom se s vámi mohli spojit na správné cestě vpřed pro vás a vaši organizaci.
