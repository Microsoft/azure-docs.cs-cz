---
title: Seznámení s úložištěm objektů BLOB (Object)
titleSuffix: Azure Storage
description: Úložiště objektů BLOB v Azure slouží k ukládání obrovských objemů nestrukturovaných dat objektů, jako jsou textová nebo binární data. Služba Azure Blob Storage je vysoce škálovatelná a dostupná.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 06/24/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b55ea814214397e12e50e7bc5047988394623940
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703585"
---
# <a name="introduction-to-azure-blob-storage"></a>Seznámení se službou Azure Blob Storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Prostředky BLOB Storage

Úložiště objektů BLOB nabízí tři typy prostředků:

- **Účet úložiště**
- **Kontejner** v účtu úložiště
- **Objekt BLOB** v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram znázorňující vztah mezi účtem úložiště, kontejnery a objekty blob](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Účty úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který je uložen v Azure Storage má adresu, která obsahuje jedinečný název účtu. Kombinace názvu účtu a koncového bodu objektu blob Azure Storage tvoří základní adresu pro objekty v účtu úložiště.

Pokud má například účet úložiště název *mystorageaccount*, pak je výchozím koncovým bodem pro úložiště objektů BLOB:

```
http://mystorageaccount.blob.core.windows.net
```

Informace o vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../common/storage-account-create.md). Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Kontejnery

Kontejner uspořádá sadu objektů blob, podobně jako adresář v systému souborů. Účet úložiště může zahrnovat neomezený počet kontejnerů a v každém kontejneru může být neomezený počet objektů blob.

> [!NOTE]
> Názvy kontejnerů musí obsahovat jenom malá písmena. Další informace o pojmenování kontejnerů najdete v tématu [pojmenování kontejnerů, objektů BLOB a metadat a odkazování](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)na ně.

### <a name="blobs"></a>Objekty blob

Azure Storage podporuje tři typy objektů BLOB:

- **Objekty blob bloku** ukládají textová a binární data. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě. Objekty blob bloku se ukládají až do přibližně 4,75 TiB dat. Větší objekty blob bloku jsou dostupné ve verzi Preview, až do přibližně 190,7 TiB.
- **Doplňovací objekty blob** se skládají z bloků, jako jsou objekty blob bloku, ale jsou optimalizované pro operace připojení. Objekty blob připojení jsou ideální pro scénáře, jako je protokolování dat z virtuálních počítačů.
- **Objekty blob stránky** ukládají soubory s náhodným přístupem až do 8 TIB velikosti. Objekty blob stránky ukládají soubory virtuálního pevného disku (VHD) a slouží jako disky pro virtuální počítače Azure. Další informace o objektech blob stránky najdete v tématu [Přehled objektů blob stránky Azure](storage-blob-pageblob-overview.md) .

Další informace o různých typech objektů BLOB najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Přesouvání dat do úložiště objektů blob

Pro migraci stávajících dat do úložiště objektů BLOB existuje několik řešení:

- **AzCopy** je snadno použitelný nástroj příkazového řádku pro Windows a Linux, který kopíruje data do a z úložiště blobů, napříč kontejnery nebo napříč účty úložiště. Další informace o AzCopy najdete v tématu [přenos dat pomocí AzCopy v10 za účelem](../common/storage-use-azcopy-v10.md).
- **Knihovna pro přesun dat Azure Storage** je knihovna .NET pro přesouvání dat mezi Azure Storagemi službami. Nástroj AzCopy je sestavený s knihovnou přesunu dat. Další informace najdete v [referenční dokumentaci](/dotnet/api/microsoft.azure.storage.datamovement) k knihovně pro přesun dat.
- **Azure Data Factory** podporuje kopírování dat do a ze služby Blob Storage pomocí klíče účtu, sdíleného přístupového podpisu, instančního objektu nebo spravovaných identit pro prostředky Azure. Další informace najdete v tématu [kopírování dat do nebo z úložiště objektů BLOB v Azure pomocí Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- **Blobfuse** je ovladač virtuálního systému souborů pro úložiště objektů BLOB v Azure. Blobfuse můžete použít pro přístup k existujícím datům objektů blob bloku v účtu úložiště prostřednictvím systému souborů Linux. Další informace najdete v tématu [Postup připojení úložiště objektů BLOB jako systému souborů pomocí blobfuse](storage-how-to-mount-container-linux.md).
- Služba **Azure Data box** je k dispozici pro přenos místních dat do úložiště objektů blob, pokud velké datové sady nebo omezení sítě odesílají data prostřednictvím nereálného datového spojení. V závislosti na velikosti dat můžete od Microsoftu požádat o [Azure Data box disk](../../databox/data-box-disk-overview.md), [Azure Data box](../../databox/data-box-overview.md)nebo [Azure Data box Heavy](../../databox/data-box-heavy-overview.md) zařízení. Data pak můžete zkopírovat do těchto zařízení a odeslat je zpátky do Microsoftu, aby se nahráli do úložiště objektů BLOB.
- **Služba Azure import/export** nabízí způsob, jak importovat nebo exportovat velké objemy dat do a z účtu úložiště pomocí pevných disků, které zadáte. Další informace najdete v tématu [použití služby Microsoft Azure import/export k přenosu dat do úložiště objektů BLOB](../../import-export/storage-import-export-service.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](scalability-targets.md)
