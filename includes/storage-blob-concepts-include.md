---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 203f5a766c4c8a8f1e577f6be1e18d0f9ac95403
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31432760"
---
Azure Blob storage je řešení úložiště od Microsoftu objektu pro cloud. Úložiště objektů blob je optimalizovaná pro ukládání masivní objemy nestrukturovaných dat, jako je například textu nebo binárních dat.

Úložiště objektů blob je ideální pro:

* Poskytování obrázků nebo dokumentů přímo do prohlížeče.
* Ukládání souborů pro distribuovaný přístup.
* Streamování videa a zvuku.
* Zápis do souborů protokolu.
* Ukládání dat pro zálohování a obnovování, zotavení po havárii a archivaci.
* Ukládání dat pro analýzu pomocí místní nebo Azure hostovaná služba.

Objekty v úložišti objektů Blob přístupná odkudkoli na světě prostřednictvím protokolu HTTP nebo HTTPS. Uživatelé nebo klientské aplikace mají přístup k objektům BLOB prostřednictvím adresy URL, [REST API pro Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/storage), nebo Klientská knihovna pro úložiště Azure. Knihovny klienta úložiště jsou k dispozici více jazyků, včetně [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/en/latest/index.html), [PHP](http://azure.github.io/azure-storage-php/), a [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="blob-service-concepts"></a>Koncepty služby Blob service

Úložiště objektů BLOB zpřístupní tři zdroje: váš účet úložiště, kontejnery v účtu a objekty BLOB v kontejneru. Následující diagram znázorňuje vztah mezi tyto prostředky.

![Diagram architektury úložiště Blob (objekt)](./media/storage-blob-concepts-include/blob1.png)

### <a name="storage-account"></a>Účet úložiště

Veškerý přístup k datové objekty ve službě Azure Storage se stane, prostřednictvím účtu úložiště. Další informace najdete v tématu [účty Azure storage](../articles/storage/common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="container"></a>Kontejner

Kontejner organizuje sady objektů BLOB, podobně jako do složky v systému souborů. Všechny objekty BLOB jsou umístěny do kontejneru. Účet úložiště může obsahovat neomezený počet kontejnerů a kontejner můžete pojmout neomezený počet objektů BLOB. Všimněte si, že název kontejneru musí být psaný malými písmeny.

### <a name="blob"></a>Objekt blob
 
Azure Storage nabízí tři typy objektů BLOB – objekty BLOB bloku, doplňovací objekty BLOB, a [stránek](../articles/storage/blobs/storage-blob-pageblob-overview.md) (používá se pro soubory virtuálního pevného disku).

* Objekty BLOB bloku ukládat textové a binární data, až o 4.7 TB. Objekty BLOB bloku se skládá z bloků dat, které lze spravovat jednotlivě.
* Append – objekty BLOB je tvoří bloky jako objekty BLOB bloku, ale jsou optimalizované pro doplňovací operace. Append – objekty BLOB jsou ideální pro scénáře, jako je protokolování dat z virtuálních počítačů.
* Náhodný přístup pro úložiště objektů BLOB stránky souborů až 8 TB s velikostí. Objekty BLOB stránky ukládat soubory virtuálního pevného disku, které zálohování virtuálních počítačů.

Všechny objekty BLOB jsou umístěny do kontejneru. Kontejner je podobný do složky v systému souborů. Další můžete uspořádat objekty BLOB do virtuálního adresáře a procházení je stejně jako systém souborů. 

V případě velkých datových sad, kde stahování nebo ukládání dat do Blob Storage přes internet není vzhledem k síťovým omezením reálné, můžete sadu pevných disků zaslat společnosti Microsoft, která data exportuje nebo importuje přímo v datovém centru. Další informace najdete v tématu [používat službu Microsoft Azure Import/Export přenos dat do úložiště objektů Blob](../articles/storage/common/storage-import-export-service.md).
  
Podrobnosti o vytváření názvů kontejnerů a objektů blob najdete v článku [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
