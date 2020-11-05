---
title: Známé problémy s Azure Data Lake Storage Gen2 | Microsoft Docs
description: Přečtěte si o omezeních a známých problémech Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 87532876551cacbd4da03b95b10c41600989a947
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359710"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Známé problémy s Azure Data Lake Storage Gen2

Tento článek popisuje omezení a známé problémy Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Podporované funkce Blob Storage

Rostoucí počet funkcí služby Blob Storage teď funguje s účty, které mají hierarchický obor názvů. Úplný seznam najdete [v tématu BLOB Storage funkce dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Podporované integrace služeb Azure

Azure Data Lake Storage Gen2 podporuje několik služeb Azure, které můžete použít k ingestování dat, provádění analýz a vytváření vizuální reprezentace. Seznam podporovaných služeb Azure najdete v tématu [služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Podívejte [se na služby Azure, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Podporované opensourcové platformy

Data Lake Storage Gen2 podporuje několik Open Source platforem. Úplný seznam najdete v tématu [Open Source Platforms, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Viz [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Rozhraní API pro úložiště objektů BLOB

Rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 můžou pracovat se stejnými daty.

Tato část popisuje problémy a omezení s použitím rozhraní API objektů BLOB a rozhraní API pro Data Lake Storage Gen2 pro práci se stejnými daty.

* Rozhraní BLOB API a rozhraní Data Lake Storage API nemůžete použít k zápisu do stejné instance souboru. Pokud zapisujete do souboru pomocí Data Lake Storage Gen2 rozhraní API, pak bloky tohoto souboru nebudou viditelné pro volání rozhraní API objektů BLOB [Get Block](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Jediná výjimka je v případě, že používáte přepsání. Soubor nebo objekt blob můžete přepsat buď pomocí rozhraní API.

* Když použijete operaci [listovat BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) bez zadání oddělovače, výsledky budou zahrnovat adresáře a objekty blob. Pokud se rozhodnete použít oddělovač, použijte pouze lomítko ( `/` ). Toto je jediný podporovaný oddělovač.

* Použijete-li k odstranění adresáře rozhraní API pro [odstranění objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) , bude tento adresář odstraněn pouze v případě, že je prázdný. To znamená, že nemůžete rekurzivně odstraňovat adresáře pomocí rozhraní BLOB API.

Tato rozhraní REST API pro objekty blob nejsou podporovaná:

* [Vložit objekt BLOB (stránka)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Vložit stránku](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Získat rozsahy stránek](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Objekt BLOB přírůstkového kopírování](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Vložit stránku z adresy URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Připojit blok z adresy URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Nespravované disky virtuálních počítačů nejsou podporované v účtech, které mají hierarchický obor názvů. Pokud chcete povolit hierarchický obor názvů v účtu úložiště, umístěte nespravované disky virtuálních počítačů do účtu úložiště, který nemá povolenou funkci hierarchického oboru názvů.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Podpora pro rekurzivní nastavení seznamů řízení přístupu (ACL)

Možnost použít rekurzivní změny seznamů ACL z nadřazeného adresáře na podřízené položky je všeobecně dostupná. V aktuální verzi této funkce můžete použít změny seznamů ACL pomocí PowerShellu, rozhraní příkazového řádku Azure a sady .NET, Java a Python SDK. Podpora není ještě k dispozici pro Azure Portal ani Průzkumník služby Azure Storage.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Použijte pouze nejnovější verzi AzCopy ([AzCopy v10 za účelem](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Starší verze AzCopy, jako je AzCopy v 8.1, nejsou podporovány.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Používejte pouze verze `1.6.0` nebo vyšší.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Průzkumník služby Storage v Azure Portal

Seznamy řízení přístupu (ACL) ještě nejsou podporované.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Aplikace třetích stran

Aplikace třetích stran, které používají rozhraní REST API k práci, budou fungovat i v případě, že je použijete s Data Lake Storage Gen2 aplikacemi, které volají rozhraní API objektů blob, budou pravděpodobně fungovat.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Seznamy řízení přístupu (ACL) a anonymní přístup pro čtení

Pokud byl kontejneru udělen [přístup anonymního přístupu pro čtení](storage-manage-access-to-resources.md) , nebudou mít seznamy ACL žádný vliv na tento kontejner nebo soubory v tomto kontejneru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

Nastavení pro dny uchování se ještě nepodporuje, ale protokoly můžete odstranit ručně pomocí libovolného podporovaného nástroje, jako je Průzkumník služby Azure Storage, REST nebo SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Zásady správy životního cyklu s úrovní Premium pro Azure Data Lake Storage

Nemůžete přesouvat data uložená na úrovni Premium mezi horkou, studenou a archivní úrovní. Data z úrovně Premium ale můžete kopírovat na úroveň Hot Access v jiném účtu.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Podpora Dremio s účty úložiště Premium-Performance BlockBlobStorage

Dremio se ještě nepřipojí k účtu BlockBlobStorage, který má povolenou funkci hierarchického oboru názvů. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Ovladač Windows Azure Storage Blob (WASB) (není podporován s Data Lake Storage Gen2)

V současné době má ovladač WASB, který byl navržen pro práci s rozhraním BLOB API, problémy v několika běžných scénářích. Konkrétně, pokud se jedná o klienta s hierarchickým účtem úložiště s povoleným oborem názvů. Přístup k více protokolům na Data Lake Storage nebude tyto problémy zmírnit. 

Po dobu (a nejvíce pravděpodobného budoucího) nebudeme zákazníkům podporovat používání ovladače WASB jako klienta k hierarchickému účtu úložiště s povoleným oborem názvů. Místo toho doporučujeme, abyste ve svém prostředí Hadoop použili ovladač [systému souborů BLOB v Azure (ABFS)](data-lake-storage-abfs-driver.md) . Pokud se snažíte migrovat z místního prostředí Hadoop s verzí starší než Hadoop, otevřete lístek podpory Azure, abyste se mohli obrátit na správnou cestu, která se vám bude týkat vaší organizace.
