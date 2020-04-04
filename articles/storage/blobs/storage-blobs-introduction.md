---
title: Úvod do úložiště objektů blob (objekt) – Azure Storage
description: Do Azure Blob Storage můžete ukládat velké objemy dat nestrukturovaných objektových dat, jako jsou textová nebo binární data. Služba Azure Blob Storage je vysoce škálovatelná a dostupná. Klienti mají k datům objektů ve službě Blob Storage přístup z PowerShellu nebo Azure CLI, programově prostřednictvím klientských knihoven Azure Storage nebo přes REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 03/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f7d1967c8a9585fbf5131da986595761030f0e5f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631998"
---
# <a name="introduction-to-azure-blob-storage"></a>Seznámení se službou Azure Blob Storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Prostředky úložiště objektů blob

Úložiště objektů blob nabízí tři typy prostředků:

- **Účet úložiště**
- **Kontejner** v účtu úložiště
- **Objekt blob** v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram znázorňující vztah mezi účtem úložiště, kontejnery a objekty BLOB](./media/storage-blobs-introduction/blob1.png)

### <a name="storage-accounts"></a>Účty úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který uložíte ve službě Azure Storage, má adresu, která obsahuje váš jedinečný název účtu. Kombinace názvu účtu a koncového bodu objektu blob azure storage tvoří základní adresu pro objekty ve vašem účtu úložiště.

Pokud se například váš účet úložiště jmenuje *mystorageaccount*, bude výchozí koncový bod pro úložiště objektů Blob:

```
http://mystorageaccount.blob.core.windows.net
```

Pokud chcete vytvořit účet úložiště, [přečtěte si tématu Vytvoření účtu úložiště](../common/storage-account-create.md). Další informace o účtech úložiště najdete v [tématu Přehled účtu úložiště Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Kontejner uspořádá sadu objektů BLOB, podobně jako adresář v systému souborů. Účet úložiště může zahrnovat neomezený počet kontejnerů a v každém kontejneru může být neomezený počet objektů blob.

> [!NOTE]
> Názvy kontejnerů musí obsahovat jenom malá písmena. Další informace o pojmenování kontejnerů naleznete [v tématu Pojmenování a odkazování kontejnerů, objektů Blob a metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Objekty blob

Azure Storage podporuje tři typy objektů BLOB:

- **Objekty BLOB** bloku ukládají text a binární data, až o 4,7 TB. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě.
- **Připojit objekty BLOB** se shodnou z bloků, jako jsou objekty BLOB bloku, ale jsou optimalizované pro operace připojení. Doplňovací objekty blob jsou ideální pro scénáře, jako je protokolování dat z virtuálních počítačů.
- **Objekty BLOB stránky** ukládají soubory náhodného přístupu o velikosti až 8 TB. Objekty BLOB stránky ukládají soubory virtuálních pevných disků (VHD) a slouží jako disky pro virtuální počítače Azure. Další informace o objektech BLOB stránky najdete v [tématu Přehled objektů blob stránky Azure.](storage-blob-pageblob-overview.md)

Další informace o různých typech objektů BLOB najdete [v tématu Principy objektů BLOB bloků, objektů BLOB připojit a objektů BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Přesouvání dat do úložiště objektů blob

Existuje řada řešení pro migraci existujících dat do úložiště objektů Blob:

- **AzCopy** je snadno použitelný nástroj příkazového řádku pro Windows a Linux, který kopíruje data do a z úložiště objektů Blob, mezi kontejnery nebo mezi účty úložiště. Další informace o AzCopy viz [Přenos dat pomocí AzCopy v10 (náhled).](../common/storage-use-azcopy-v10.md)
- **Knihovna Azure Storage Data Movement** je knihovna .NET pro přesouvání dat mezi službami Azure Storage. Nástroj AzCopy je vytvořen s knihovnou Přesun dat. Další informace naleznete v [referenční dokumentaci](/dotnet/api/microsoft.azure.storage.datamovement) pro knihovnu Přesun dat.
- **Azure Data Factory** podporuje kopírování dat do úložiště objektů Blob a z úložiště pomocí klíče účtu, sdíleného přístupového podpisu, instančního objektu nebo spravovaných identit pro prostředky Azure. Další informace najdete [v tématu Kopírování dat do úložiště objektů blob Azure nebo z něj pomocí Azure Data Factory](../../data-factory/connector-azure-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
- **Blobfuse** je ovladač virtuálního systému souborů pro úložiště objektů Blob Azure. Blobfuse můžete použít pro přístup k existujícím datům blob bloku v účtu úložiště prostřednictvím systému souborů Linux. Další informace naleznete v tématu [Jak připojit úložiště objektů Blob jako systém souborů s blobfuse](storage-how-to-mount-container-linux.md).
- **Služba Azure Data Box** je k dispozici pro přenos místních dat do úložiště objektů Blob, když velké datové sady nebo síťová omezení činí nahrávání dat po drátě nerealistickým. V závislosti na velikosti dat můžete od [Microsoftu](../../databox/data-box-disk-overview.md)požadovat disk Azure Data Box , [Azure Data Box](../../databox/data-box-overview.md)nebo zařízení Azure Data Box [Heavy.](../../databox/data-box-heavy-overview.md) Potom můžete data zkopírovat do těchto zařízení a odeslat je zpět do Microsoftu a nahrát je do úložiště objektů Blob.
- **Služba Import a export Azure** poskytuje způsob importu nebo exportu velkého množství dat do a z vašeho účtu úložiště pomocí pevných disků, které poskytujete. Další informace najdete [v tématu Přenos dat do úložiště objektů Blob pomocí služby Import a export Microsoft Azure](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Škálovatelnost a výkonnostní cíle pro úložiště objektů Blob](scalability-targets.md)
