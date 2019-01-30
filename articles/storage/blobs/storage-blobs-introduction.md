---
title: Seznámení se službou Blob Storage – úložiště objektů v Azure
description: Do Azure Blob Storage můžete ukládat velké objemy dat nestrukturovaných objektových dat, jako jsou textová nebo binární data. Služba Azure Blob Storage je vysoce škálovatelná a dostupná. Klienti mají k datům objektů ve službě Blob Storage přístup z PowerShellu nebo Azure CLI, programově prostřednictvím klientských knihoven Azure Storage nebo přes REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 01/03/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 761d5fe52222331a26f85cdb2787a50a89504021
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240943"
---
# <a name="introduction-to-azure-blob-storage"></a>Seznámení se službou Azure Blob Storage

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Prostředky úložiště objektů BLOB

Úložiště objektů BLOB nabízí tři typy prostředků:

- **Účtu úložiště**. 
- A **kontejneru** v účtu úložiště
- A **blob** v kontejneru 

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště objektů Blob](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Účty úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, ve kterém je uložený ve službě Azure Storage má adresu, která obsahuje název vaší jedinečný účet. Kombinace názvu účtu a koncový bod služby Azure Storage blob tvoří základní adresu pro objekty ve vašem účtu úložiště.

Například, pokud je název vašeho účtu úložiště *mystorageaccount*, pak bude výchozím koncovým bodem pro úložiště objektů Blob:

```
http://mystorageaccount.blob.core.windows.net 
```

Vytvoření účtu úložiště najdete v tématu [vytvořit účet úložiště](../common/storage-quickstart-create-account.md). Další informace o účtech úložiště najdete v tématu [přehled účtu Azure storage](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Kontejner slouží k uspořádání sady objektů BLOB, podobně jako do jiného adresáře v systému souborů. Účet úložiště může zahrnovat neomezený počet kontejnerů a v každém kontejneru může být neomezený počet objektů blob. 

  > [!NOTE]
  > Názvy kontejnerů musí obsahovat jenom malá písmena. Další informace o pojmenování kontejnerů najdete v tématu [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Objekty blob
 
Azure Storage podporuje tři typy objektů blob:

* **Objekty BLOB bloku** ukládání textových a binárních dat, a to až do velikosti 4,7 TB. Objekty blob bloku se skládají z bloků dat, které můžete spravovat jednotlivě.
* **Doplňovací objekty BLOB** jsou tvořené bloky, jako jsou objekty BLOB bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekty blob jsou ideální pro scénáře, jako je protokolování dat z virtuálních počítačů.
* **Objekty BLOB stránky** náhodný přístup úložiště souborů až do 8 TB velikosti. Úložiště, které soubory virtuálního pevného disku (VHD) bude sloužit jako disky pro virtuální počítače Azure, objekty BLOB stránky. Další informace o objektech BLOB stránky, najdete v článku [objekty BLOB stránky Azure – přehled](storage-blob-pageblob-overview.md)

Další informace o různých typech objektů BLOB najdete v tématu [vysvětlení objektů BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Přesun dat do úložiště objektů Blob

Počet řešení existují pro migraci existujících dat do úložiště objektů Blob:

- **AzCopy** je nástroj příkazového řádku snadno použitelné pro Windows a Linux, který kopíruje data do a z úložiště objektů Blob, napříč kontejnery nebo mezi různými účty úložiště. Další informace o AzCopy najdete v tématu [přenos dat pomocí AzCopy v10 (Preview)](../common/storage-use-azcopy-v10.md). 
- **Knihovna pro přesun dat úložiště Azure** je knihovna .NET pro přesun dat mezi službami Azure Storage. Knihovna pro přesun dat je sestavován nástroj AzCopy. Další informace najdete v tématu [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement) pro přesun dat knihovny. 
- **Azure Data Factory** podporuje kopírování dat do a z úložiště objektů Blob s použitím klíče účtu, sdílený přístupový podpis službu objektu zabezpečení nebo spravovaných identit pro ověřování prostředků Azure. Další informace najdete v tématu [kopírování dat do nebo z úložiště objektů Blob v Azure pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** je virtuálním souborovém systému ovladače pro úložiště objektů Blob v Azure. Blobfuse můžete použít pro přístup k existující data objektů blob bloku v účtu úložiště v systému Linux. Další informace najdete v tématu [postup připojení služby Blob storage jako systém souborů s blobfuse](storage-how-to-mount-container-linux.md).
- **Azure Data Box Disk** je služba pro přenos místních dat do úložiště objektů Blob při velkých datových sad nebo síťovým omezením nahrávání dat při přenosu nereálný. Můžete využít [Azure Data Box Disk](../../databox/data-box-disk-overview.md) a vyžádat si od Microsoftu jednotky SSD (solid-state drive). Potom můžete data zkopírovat na tyto disky a odeslat je do Microsoftu, aby se nahrála do služby Blob Storage.
- **Služba Azure Import/Export** poskytuje způsob, jak export velkých objemů dat z vašeho účtu úložiště do pevné disky, které zadáte, a dodává se pak vrátit k tomu se svými daty, které Microsoft. Další informace najdete v tématu [přenášet data do úložiště objektů Blob pomocí služby Microsoft Azure Import/Export](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Další postup

* [Vytvoření účtu úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure škálovatelnost a výkonnostní cíle Storage](../common/storage-scalability-targets.md)
